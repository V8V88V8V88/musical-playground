<script>
  import { onMount } from 'svelte';
  import * as Tone from 'tone';

  let synth;
  let activeStrings = new Set();
  let strings = [
    { note: 'E2', key: 'A', color: 'amber-600' },
    { note: 'A2', key: 'S', color: 'amber-500' },
    { note: 'D3', key: 'D', color: 'amber-400' },
    { note: 'G3', key: 'F', color: 'amber-300' },
    { note: 'B3', key: 'G', color: 'amber-200' },
    { note: 'E4', key: 'H', color: 'amber-100' },
  ];

  onMount(() => {
    synth = new Tone.Synth({
      oscillator: {
        type: 'sawtooth8',
      },
      envelope: {
        attack: 0.005,
        decay: 0.1,
        sustain: 0.3,
        release: 1,
      },
    }).toDestination();

    const handleKeyDown = (event) => {
      if (event.repeat) return;
      const key = event.key.toUpperCase();
      const stringData = strings.find((s) => s.key === key);
      if (stringData) {
        playString(stringData.note, stringData.key);
      }
    };

    const handleKeyUp = (event) => {
      const key = event.key.toUpperCase();
      activeStrings.delete(key);
    };

    window.addEventListener('keydown', handleKeyDown);
    window.addEventListener('keyup', handleKeyUp);

    return () => {
      window.removeEventListener('keydown', handleKeyDown);
      window.removeEventListener('keyup', handleKeyUp);
    };
  });

  function playString(note, key) {
    synth.triggerAttackRelease(note, '2n');
    activeStrings.add(key);
    setTimeout(() => {
      activeStrings.delete(key);
    }, 200);
  }
</script>

<div class="flex flex-col items-center space-y-6 p-8">
  <div class="w-full max-w-2xl">
    {#each strings as { note, key, color }}
      <button
        class={`relative w-full h-16 bg-gradient-to-r from-amber-800 to-amber-700 rounded-lg mb-4 overflow-hidden transition-transform duration-200 transform hover:scale-105 focus:outline-none`}
        class:scale-105={activeStrings.has(key)}
        on:click={() => playString(note, key)}
      >
        <div class="absolute inset-0 flex items-center justify-between px-6">
          <div class="flex items-center space-x-4">
            <span class="text-xl font-bold text-white">{note}</span>
            <span class="px-3 py-1 bg-white/20 rounded-full text-sm text-white">
              Press {key}
            </span>
          </div>
          <div class="h-px flex-1 mx-8 bg-gradient-to-r from-transparent via-amber-400/30 to-transparent"></div>
          <div class="w-4 h-4 rounded-full bg-amber-400/80 shadow-lg"></div>
        </div>
        <div
          class="absolute inset-0 transform origin-left transition-transform duration-300"
          style="background-color: rgba(var(--tw-color-${color.replace('amber-', '')}), 0.2);"
          class:scale-x-100={activeStrings.has(key)}
        ></div>
      </button>
    {/each}
  </div>
</div>
