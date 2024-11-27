<script>
    import { onMount } from 'svelte';
    import * as Tone from 'tone';
  
    let synth;
    let activeKeys = new Set();
    let keys = [
      { note: 'C4', key: 'A', color: 'white' },
      { note: 'C#4', key: 'W', color: 'black' },
      { note: 'D4', key: 'S', color: 'white' },
      { note: 'D#4', key: 'E', color: 'black' },
      { note: 'E4', key: 'D', color: 'white' },
      { note: 'F4', key: 'F', color: 'white' },
      { note: 'F#4', key: 'T', color: 'black' },
      { note: 'G4', key: 'G', color: 'white' },
      { note: 'G#4', key: 'Y', color: 'black' },
      { note: 'A4', key: 'H', color: 'white' },
      { note: 'A#4', key: 'U', color: 'black' },
      { note: 'B4', key: 'J', color: 'white' },
    ];
  
    onMount(() => {
      synth = new Tone.Synth({
        oscillator: {
          type: 'triangle'
        },
        envelope: {
          attack: 0.005,
          decay: 0.1,
          sustain: 0.3,
          release: 1
        }
      }).toDestination();
      
      window.addEventListener('keydown', handleKeyDown);
      window.addEventListener('keyup', handleKeyUp);
      
      return () => {
        window.removeEventListener('keydown', handleKeyDown);
        window.removeEventListener('keyup', handleKeyUp);
      };
    });
  
    function playNote(note, keyLabel) {
      synth.triggerAttack(note);
      activeKeys.add(keyLabel);
      activeKeys = activeKeys;
    }
  
    function stopNote(note, keyLabel) {
      synth.triggerRelease();
      activeKeys.delete(keyLabel);
      activeKeys = activeKeys;
    }
  
    function handleKeyDown(event) {
      if (event.repeat) return;
      const key = event.key.toUpperCase();
      const keyData = keys.find(k => k.key === key);
      if (keyData) {
        playNote(keyData.note, keyData.key);
      }
    }
  
    function handleKeyUp(event) {
      const key = event.key.toUpperCase();
      const keyData = keys.find(k => k.key === key);
      if (keyData) {
        stopNote(keyData.note, keyData.key);
      }
    }
  </script>
  
  <div class="relative flex justify-center px-4 py-8">
    <div class="flex">
      {#each keys as { note, key, color }}
        <button
          class="relative {color === 'white' 
            ? 'w-16 h-56 bg-white hover:bg-amber-50 border border-amber-200 rounded-b-lg' 
            : 'w-10 h-36 bg-amber-900 hover:bg-amber-800 -mx-5 z-10'} 
            transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-amber-500 
            {activeKeys.has(key) ? (color === 'white' ? 'bg-amber-100' : 'bg-amber-700') : ''}"
          on:mousedown={() => playNote(note, key)}
          on:mouseup={() => stopNote(note, key)}
          on:mouseleave={() => activeKeys.has(key) && stopNote(note, key)}
        >
          <span class="absolute bottom-4 left-1/2 transform -translate-x-1/2 text-sm font-medium
            {color === 'white' ? 'text-amber-900' : 'text-amber-100'}">
            {key}
          </span>
        </button>
      {/each}
    </div>
  </div>
  