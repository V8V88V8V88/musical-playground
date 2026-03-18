<script>
  import { onMount } from 'svelte';
  import * as Tone from 'tone';

  let activeKeys = new Set();
  let audioContextStarted = false;

  let drums = [
    { name: 'Kick', key: 'A', sound: 'kick', color: 'amber' },
    { name: 'Snare', key: 'S', sound: 'snare', color: 'yellow' },
    { name: 'Hi-Hat', key: 'D', sound: 'hihat', color: 'orange' },
    { name: 'Tom', key: 'F', sound: 'tom', color: 'red' },
    { name: 'Crash', key: 'G', sound: 'crash', color: 'purple' },
  ];

  let synths = {};

  onMount(() => {
    console.log("Initializing drum machine synths...");

    synths.kick = new Tone.MembraneSynth().toDestination();
    synths.snare = new Tone.NoiseSynth({
      noise: { type: "white" },
      envelope: { attack: 0.005, decay: 0.2, sustain: 0, release: 0.2 }
    }).toDestination();
    synths.hihat = new Tone.MetalSynth({
      frequency: 200,
      envelope: { attack: 0.001, decay: 0.1, release: 0.01 },
      harmonicity: 5.1,
      modulationIndex: 32,
      resonance: 4000,
      octaves: 1.5
    }).toDestination();
    synths.tom = new Tone.MembraneSynth({
      pitchDecay: 0.05,
      octaves: 4,
      oscillator: { type: "sine" },
      envelope: { attack: 0.001, decay: 0.4, sustain: 0.01, release: 1.4 }
    }).toDestination();
    synths.crash = new Tone.MetalSynth({
      frequency: 300,
      envelope: { attack: 0.001, decay: 1.5, release: 0.2 },
      harmonicity: 5.1,
      modulationIndex: 32,
      resonance: 4000,
      octaves: 1.5
    }).toDestination();

    // Attach event listeners
    window.addEventListener('keydown', handleKeyDown);
    window.addEventListener('keyup', handleKeyUp);

    return () => {
      window.removeEventListener('keydown', handleKeyDown);
      window.removeEventListener('keyup', handleKeyUp);
      
      // dispose synths
      Object.values(synths).forEach(synth => {
        if (synth && synth.dispose) synth.dispose();
      });
    };
  });

  async function ensureAudioContext() {
    if (!audioContextStarted) {
      await Tone.start();
      console.log("Audio context started");
      audioContextStarted = true;
    }
  }

  function playDrum(sound, key) {
    console.log(`Attempting to play: ${sound}`);
    if (synths[sound]) {
      if (sound === 'kick') synths[sound].triggerAttackRelease("C1", "8n");
      else if (sound === 'snare') synths[sound].triggerAttackRelease("8n");
      else if (sound === 'hihat') synths[sound].triggerAttackRelease("32n");
      else if (sound === 'tom') synths[sound].triggerAttackRelease("G2", "4n");
      else if (sound === 'crash') synths[sound].triggerAttackRelease("2n");
      
      activeKeys.add(key);
      activeKeys = new Set(activeKeys); // Trigger reactivity
      setTimeout(() => {
        activeKeys.delete(key);
        activeKeys = new Set(activeKeys); // Trigger reactivity
      }, 100);
    } else {
      console.error(`Synth for ${sound} not loaded`);
    }
  }

  async function handleKeyDown(event) {
    if (event.repeat) return;
    await ensureAudioContext();
    const key = event.key.toUpperCase();
    const drum = drums.find((d) => d.key === key);
    if (drum) {
      playDrum(drum.sound, drum.key);
    }
  }

  function handleKeyUp(event) {
    const key = event.key.toUpperCase();
    activeKeys.delete(key);
    activeKeys = new Set(activeKeys); // Trigger reactivity
  }
</script>

<div class="grid grid-cols-2 md:grid-cols-3 gap-6 p-4">
  {#each drums as { name, key, sound, color }}
    <button
      class="group relative overflow-hidden rounded-2xl bg-gradient-to-br transition-transform duration-200 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-amber-500"
      class:from-amber-400={color === 'amber'}
      class:to-amber-600={color === 'amber'}
      class:from-yellow-400={color === 'yellow'}
      class:to-yellow-600={color === 'yellow'}
      class:from-orange-400={color === 'orange'}
      class:to-orange-600={color === 'orange'}
      class:from-red-400={color === 'red'}
      class:to-red-600={color === 'red'}
      class:from-purple-400={color === 'purple'}
      class:to-purple-600={color === 'purple'}
      class:scale-95={activeKeys.has(key)}
      on:click={() => { ensureAudioContext(); playDrum(sound, key); }}
    >
      <div class="aspect-square flex flex-col items-center justify-center p-6">
        <span class="text-2xl font-bold text-white mb-2">{name}</span>
        <span class="px-3 py-1 bg-white/20 rounded-full text-sm text-white">
          Press {key}
        </span>
      </div>
      <div class="absolute inset-0 bg-white/20 transform origin-bottom transition-transform duration-300 group-hover:scale-y-100 scale-y-0"></div>
    </button>
  {/each}
</div>
