<script>
    import { onMount } from 'svelte';
    import * as Tone from 'tone';
  
    let audioBuffers = {};
    let activeKeys = new Set();
    let drums = [
      { name: 'Kick', key: 'A', sound: 'kick', color: 'amber' },
      { name: 'Snare', key: 'S', sound: 'snare', color: 'yellow' },
      { name: 'Hi-Hat', key: 'D', sound: 'hihat', color: 'orange' },
      { name: 'Tom', key: 'F', sound: 'tom', color: 'red' },
      { name: 'Crash', key: 'G', sound: 'crash', color: 'purple' },
    ];
  
    onMount(async () => {
      await Tone.start();
      
      const soundUrls = {
        kick: "https://tonejs.github.io/audio/drum-samples/kick.mp3",
        snare: "https://tonejs.github.io/audio/drum-samples/snare.mp3",
        hihat: "https://tonejs.github.io/audio/drum-samples/hihat.mp3",
        tom: "https://tonejs.github.io/audio/drum-samples/tom.mp3",
        crash: "https://tonejs.github.io/audio/drum-samples/crash.mp3",
      };
  
      for (const [name, url] of Object.entries(soundUrls)) {
        audioBuffers[name] = new Tone.Buffer(url, () => {
          console.log(`${name} loaded`);
        });
      }
  
      window.addEventListener('keydown', handleKeyDown);
      window.addEventListener('keyup', handleKeyUp);
      
      return () => {
        window.removeEventListener('keydown', handleKeyDown);
        window.removeEventListener('keyup', handleKeyUp);
      };
    });
  
    function playDrum(sound, key) {
      if (audioBuffers[sound] && audioBuffers[sound].loaded) {
        const player = new Tone.Player(audioBuffers[sound]).toDestination();
        player.start();
        activeKeys.add(key);
        activeKeys = activeKeys;
        setTimeout(() => {
          activeKeys.delete(key);
          activeKeys = activeKeys;
        }, 100);
      } else {
        console.log(`Sound ${sound} not loaded yet`);
      }
    }
  
    function handleKeyDown(event) {
      if (event.repeat) return;
      const key = event.key.toUpperCase();
      const drumData = drums.find(d => d.key === key);
      if (drumData) {
        playDrum(drumData.sound, drumData.key);
      }
    }
  
    function handleKeyUp(event) {
      const key = event.key.toUpperCase();
      activeKeys.delete(key);
      activeKeys = activeKeys;
    }
  </script>
  
  <div class="grid grid-cols-2 md:grid-cols-3 gap-6 p-4">
    {#each drums as { name, key, sound, color }}
      <button
        class="group relative overflow-hidden rounded-2xl bg-gradient-to-br transition-transform duration-200 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-amber-500
          {color === 'amber' ? 'from-amber-400 to-amber-600' :
           color === 'yellow' ? 'from-yellow-400 to-yellow-600' :
           color === 'orange' ? 'from-orange-400 to-orange-600' :
           color === 'red' ? 'from-red-400 to-red-600' :
           'from-purple-400 to-purple-600'}
          {activeKeys.has(key) ? 'scale-95' : ''}"
        on:click={() => playDrum(sound, key)}
      >
        <div class="aspect-square flex flex-col items-center justify-center p-6">
          <span class="text-2xl font-bold text-white mb-2">{name}</span>
          <span class="px-3 py-1 bg-white/20 rounded-full text-sm text-white">
            Press {key}
          </span>
        </div>
        <div class="absolute inset-0 bg-white/20 transform origin-bottom transition-transform duration-300 group-hover:scale-y-100 scale-y-0">
        </div>
      </button>
    {/each}
  </div>
  
  