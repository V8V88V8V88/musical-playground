<script>
    import { onMount } from 'svelte';
    import * as Tone from 'tone';
    import Piano from '../components/Piano.svelte';
    import Drums from '../components/Drums.svelte';
    import Guitar from '../components/Guitar.svelte';
  
    let currentInstrument = 'piano';
  
    onMount(() => {
      Tone.start();
    });
  
    function changeInstrument(instrument) {
      currentInstrument = instrument;
    }
  </script>
  
  <main class="min-h-screen bg-gradient-to-br from-indigo-500 to-purple-600 p-8">
    <div class="max-w-4xl mx-auto">
      <h1 class="text-4xl font-bold text-white mb-8 text-center">Musical Playground</h1>
      
      <div class="bg-white rounded-lg shadow-lg p-6 mb-8">
        <div class="flex justify-center space-x-4 mb-6">
          <button
            class="px-4 py-2 rounded-full transition-colors duration-200 {currentInstrument === 'piano' ? 'bg-indigo-600 text-white' : 'bg-gray-200 text-gray-800 hover:bg-indigo-100'}"
            on:click={() => changeInstrument('piano')}
          >
            Piano
          </button>
          <button
            class="px-4 py-2 rounded-full transition-colors duration-200 {currentInstrument === 'drums' ? 'bg-indigo-600 text-white' : 'bg-gray-200 text-gray-800 hover:bg-indigo-100'}"
            on:click={() => changeInstrument('drums')}
          >
            Drums
          </button>
          <button
            class="px-4 py-2 rounded-full transition-colors duration-200 {currentInstrument === 'guitar' ? 'bg-indigo-600 text-white' : 'bg-gray-200 text-gray-800 hover:bg-indigo-100'}"
            on:click={() => changeInstrument('guitar')}
          >
            Guitar
          </button>
        </div>
        
        {#if currentInstrument === 'piano'}
          <Piano />
        {:else if currentInstrument === 'drums'}
          <Drums />
        {:else if currentInstrument === 'guitar'}
          <Guitar />
        {/if}
      </div>
      
      <p class="text-center text-white">
        Use your mouse to click on the instruments or your keyboard to play!
      </p>
    </div>
  </main>
  
  <style lang="postcss">
    :global(body) {
      @apply bg-gray-100;
    }
  </style>