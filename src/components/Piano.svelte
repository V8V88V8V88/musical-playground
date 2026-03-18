<script>
    import { onMount, onDestroy } from 'svelte';
    import * as Tone from 'tone';
  
    let sampler;
    let activeKeys = new Set();
    let isLoaded = false;
    let audioContextStarted = false;

    // A classic piano layout mapping keys to standard notes
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
      // Use Tone.Sampler for high-quality, realistic piano sounds
      sampler = new Tone.Sampler({
        urls: {
          A0: "A0.mp3",
          C1: "C1.mp3",
          "D#1": "Ds1.mp3",
          "F#1": "Fs1.mp3",
          A1: "A1.mp3",
          C2: "C2.mp3",
          "D#2": "Ds2.mp3",
          "F#2": "Fs2.mp3",
          A2: "A2.mp3",
          C3: "C3.mp3",
          "D#3": "Ds3.mp3",
          "F#3": "Fs3.mp3",
          A3: "A3.mp3",
          C4: "C4.mp3",
          "D#4": "Ds4.mp3",
          "F#4": "Fs4.mp3",
          A4: "A4.mp3",
          C5: "C5.mp3",
          "D#5": "Ds5.mp3",
          "F#5": "Fs5.mp3",
          A5: "A5.mp3",
          C6: "C6.mp3",
          "D#6": "Ds6.mp3",
          "F#6": "Fs6.mp3",
          A6: "A6.mp3",
          C7: "C7.mp3",
          "D#7": "Ds7.mp3",
          "F#7": "Fs7.mp3",
          A7: "A7.mp3",
          C8: "C8.mp3"
        },
        release: 1,
        baseUrl: "https://tonejs.github.io/audio/salamander/",
        onload: () => {
          console.log("Piano samples loaded");
          isLoaded = true;
          // Add some light reverb
          const reverb = new Tone.Reverb({ decay: 1.5, preDelay: 0.01 }).toDestination();
          sampler.connect(reverb);
        }
      }).toDestination();
      
      window.addEventListener('keydown', handleKeyDown);
      window.addEventListener('keyup', handleKeyUp);
    });

    onDestroy(() => {
      if (typeof window !== 'undefined') {
        window.removeEventListener('keydown', handleKeyDown);
        window.removeEventListener('keyup', handleKeyUp);
      }
      if (sampler) sampler.dispose();
    });
  
    async function ensureAudioContext() {
      if (!audioContextStarted) {
        await Tone.start();
        audioContextStarted = true;
      }
    }

    function playNote(note, keyLabel) {
      if (!isLoaded) return;
      ensureAudioContext();
      sampler.triggerAttack(note);
      activeKeys.add(keyLabel);
      activeKeys = new Set(activeKeys);
    }
  
    function stopNote(note, keyLabel) {
      if (!isLoaded) return;
      sampler.triggerRelease(note);
      activeKeys.delete(keyLabel);
      activeKeys = new Set(activeKeys);
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
  
  <div class="relative flex flex-col justify-center items-center px-4 py-8">
    {#if !isLoaded}
      <div class="mb-4 text-amber-800 font-semibold animate-pulse">Loading Piano Sounds...</div>
    {/if}
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
  
  