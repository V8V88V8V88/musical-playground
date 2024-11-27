<script>
    import { onMount } from 'svelte';
    import { fade, fly } from 'svelte/transition';
    import * as Tone from 'tone';
    import Piano from '../components/Piano.svelte';
    import Drums from '../components/Drums.svelte';
    import Guitar from '../components/Guitar.svelte';
  
    let currentInstrument = 'piano';
    let isLoaded = false;
  
    onMount(() => {
      Tone.start();
      isLoaded = true;
    });
  
    function changeInstrument(instrument) {
      currentInstrument = instrument;
    }
  </script>
  
  <main class="min-h-screen bg-gradient-to-br from-yellow-300 via-yellow-200 to-amber-100">
    <div class="max-w-5xl mx-auto px-4 py-12">
      {#if isLoaded}
        <div in:fade={{ duration: 1000 }}>
          <h1 class="text-6xl font-bold text-amber-900 mb-2 text-center tracking-tight">
            Musical Playground
          </h1>
          <p class="text-amber-800 text-center mb-12 text-lg">
            Create beautiful melodies with virtual instruments
          </p>
          
          <div class="bg-white/80 backdrop-blur-lg rounded-3xl shadow-2xl p-8 mb-8">
            <div class="flex justify-center space-x-4 mb-12">
              {#each ['piano', 'drums', 'guitar'] as instrument}
                <button
                  class="px-6 py-3 rounded-full text-lg font-medium transition-all duration-300 transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-amber-500 {
                    currentInstrument === instrument
                      ? 'bg-amber-500 text-white shadow-lg'
                      : 'bg-amber-100 text-amber-900 hover:bg-amber-200'
                  }"
                  on:click={() => changeInstrument(instrument)}
                >
                  {instrument.charAt(0).toUpperCase() + instrument.slice(1)}
                </button>
              {/each}
            </div>
            
            <div class="transition-all duration-500">
              {#if currentInstrument === 'piano'}
                <div in:fly={{ y: 20, duration: 500 }}>
                  <Piano />
                </div>
              {:else if currentInstrument === 'drums'}
                <div in:fly={{ y: 20, duration: 500 }}>
                  <Drums />
                </div>
              {:else if currentInstrument === 'guitar'}
                <div in:fly={{ y: 20, duration: 500 }}>
                  <Guitar />
                </div>
              {/if}
            </div>
          </div>
          
          <div class="text-center">
            <p class="text-amber-800 text-lg font-medium mb-4">
              Use your mouse to click on the instruments or your keyboard to play!
            </p>
            <div class="inline-flex items-center space-x-2 bg-amber-100 px-4 py-2 rounded-full text-amber-900">
              <span class="text-amber-600">Tip:</span>
              <span>Press the highlighted keys on your keyboard to play notes</span>
            </div>
          </div>
        </div>
      {/if}
    </div>
  </main>
  
  <style>
    :global(body) {
      overflow-x: hidden;
    }
  </style>
  
  