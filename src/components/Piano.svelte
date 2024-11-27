<script>
    import { onMount } from 'svelte';
    import * as Tone from 'tone';
  
    let synth;
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
      synth = new Tone.Synth().toDestination();
      
      window.addEventListener('keydown', handleKeyDown);
      window.addEventListener('keyup', handleKeyUp);
      
      return () => {
        window.removeEventListener('keydown', handleKeyDown);
        window.removeEventListener('keyup', handleKeyUp);
      };
    });
  
    function playNote(note) {
      synth.triggerAttack(note);
    }
  
    function stopNote(note) {
      synth.triggerRelease();
    }
  
    function handleKeyDown(event) {
      const key = event.key.toUpperCase();
      const keyData = keys.find(k => k.key === key);
      if (keyData) {
        playNote(keyData.note);
      }
    }
  
    function handleKeyUp(event) {
      const key = event.key.toUpperCase();
      const keyData = keys.find(k => k.key === key);
      if (keyData) {
        stopNote(keyData.note);
      }
    }
  </script>
  
  <div class="flex justify-center">
    {#each keys as { note, key, color }}
      <button
        class="w-12 h-48 border border-gray-300 focus:outline-none {color === 'white' ? 'bg-white hover:bg-gray-100' : 'bg-black text-white hover:bg-gray-800'} {color === 'black' ? 'w-8 h-32 -mx-4 z-10' : ''}"
        on:mousedown={() => playNote(note)}
        on:mouseup={() => stopNote(note)}
        on:mouseleave={() => stopNote(note)}
      >
        <span class="block mt-36 text-sm {color === 'black' ? 'mt-24' : ''}">{key}</span>
      </button>
    {/each}
  </div>