<script>
    import { onMount } from 'svelte';
    import * as Tone from 'tone';
  
    let sampler;
    let activeKeys = new Set();
    let drums = [
      { name: 'Kick', key: 'A', sound: 'C2', color: 'amber' },
      { name: 'Snare', key: 'S', sound: 'D2', color: 'yellow' },
      { name: 'Hi-Hat', key: 'D', sound: 'F#2', color: 'orange' },
      { name: 'Tom', key: 'F', sound: 'A2', color: 'red' },
      { name: 'Crash', key: 'G', sound: 'C3', color: 'purple' },
    ];
  
    onMount(() => {
      sampler = new Tone.Sampler({
        urls: {
          C2: "https://tonejs.github.io/audio/drum-samples/kicks/kick.wav",
          D2: "https://tonejs.github.io/audio/drum-samples/snare/snare.wav",
          "F#2": "https://tonejs.github.io/audio/drum-samples/hh/hh.wav",
          A2: "https://tonejs.github.io/audio/drum-samples/tom/tom.wav",
          C3: "https://tonejs.github.io/audio/drum-samples/crash/crash.wav",
        },
        onload: () => {
          console.log("Sampler loaded!");
        },
      }).toDestination();
  
      window.addEventListener('keydown', handleKeyDown);
      window.addEventListener('keyup', handleKeyUp);
      
      return () => {
        window.removeEventListener('keydown', handleKeyDown);
        window.removeEventListener('keyup', handleKeyUp);
      };
    });
  
    function playDrum(sound, key) {
      sampler.triggerAttackRelease(sound, '8n');
      activeKeys.add(key);
      activeKeys = activeKeys;
      setTimeout(() => {
        activeKeys.delete(key);
        activeKeys = activeKeys;
      }, 100);
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
  
  