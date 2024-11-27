<script>
    import { onMount } from 'svelte';
    import * as Tone from 'tone';
  
    let sampler;
    let drums = [
      { name: 'Kick', key: 'A', sound: 'C2' },
      { name: 'Snare', key: 'S', sound: 'D2' },
      { name: 'Hi-Hat', key: 'D', sound: 'F#2' },
      { name: 'Tom', key: 'F', sound: 'A2' },
      { name: 'Crash', key: 'G', sound: 'C3' },
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
      
      return () => {
        window.removeEventListener('keydown', handleKeyDown);
      };
    });
  
    function playDrum(sound) {
      sampler.triggerAttackRelease(sound, '8n');
    }
  
    function handleKeyDown(event) {
      const key = event.key.toUpperCase();
      const drumData = drums.find(d => d.key === key);
      if (drumData) {
        playDrum(drumData.sound);
      }
    }
  </script>
  
  <div class="grid grid-cols-3 gap-4">
    {#each drums as { name, key, sound }}
      <button
        class="bg-indigo-500 hover:bg-indigo-600 text-white font-bold py-4 px-6 rounded-lg transition-colors duration-200"
        on:click={() => playDrum(sound)}
      >
        <span class="block text-xl mb-1">{name}</span>
        <span class="block text-sm">({key})</span>
      </button>
    {/each}
  </div>