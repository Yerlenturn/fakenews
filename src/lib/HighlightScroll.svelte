<script lang="ts">
  import { onMount } from 'svelte';
  
  // State variables using Svelte 5's $state syntax
  let state = $state({
    words: ['word1 ', 'word2 ', 'word3 ', 'word4 ', 'word5 ', 'word6. ', 'word7 ', 'word8 ', 'word9 ', 'word1 ', 'word2 ', 'word3 ', 'word4 ', 'word5 ', 'word6. ', 'word7 ', 'word8 ', 'word9 '],
    highlightedIndex: -1,
    animationStarted: false
  });
  
  // Element reference for the container
  let containerElement: HTMLElement;
  
  function updateHighlight() {
    if (!containerElement) return;
    
    const startOffset = 100;
    const speedMultiplier = 2;
    
    const containerTop = containerElement.getBoundingClientRect().top;
    const containerBottom = containerElement.getBoundingClientRect().bottom;
    const viewportHeight = window.innerHeight;
    
    if (containerTop < viewportHeight - startOffset && containerBottom > 0) {
      // Calculate which word to highlight
      const visibleProgress = Math.max(
        0, 
        (viewportHeight - containerTop - startOffset) / (viewportHeight + containerElement.offsetHeight)
      );

      const overallProgress = visibleProgress * state.words.length * speedMultiplier; // e.g., 3.4
      const currentWordIndex = Math.floor(overallProgress); // e.g. 3
      const currentWordProgress = overallProgress - currentWordIndex; // e.g. 0.4
      console.log(currentWordProgress);
      
      state.highlightedIndex = Math.min(
        Math.floor(visibleProgress * state.words.length * speedMultiplier),
        state.words.length - 1
      );
    } else {
      // Reset highlight if not in viewport
      state.highlightedIndex = -1;
    }
  }
  
  function observeContainer() {
    if (!containerElement) return;
    
    const observer = new IntersectionObserver((entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting && !state.animationStarted) {
          state.animationStarted = true;
          window.addEventListener('scroll', updateHighlight);
          updateHighlight();
        }
      });
    }, { threshold: 0.1 });
    
    observer.observe(containerElement);
    
    return () => {
      observer.disconnect();
      window.removeEventListener('scroll', updateHighlight);
    };
  }
  
  onMount(() => {
    observeContainer();
    return () => {
      window.removeEventListener('scroll', updateHighlight);
    };
  });
</script>

<div class="text-stroke-effect-5 flex-5/12" bind:this={containerElement}>
  {#each state.words as word, index}
    <span class={state.highlightedIndex >= index ? 'highlighted' : ''}>
      {word}
    </span>
  {/each}
</div>

<style>
  .text-stroke-effect-5 {
    display: flex;
    flex-wrap: wrap;
    gap: 5px; /* Reduced gap between words */
    justify-content: center;
    text-align: center;
  }
  
  /* Default Word Styling */
  .text-stroke-effect-5 span {
    position: relative;
    display: inline-block;
    font-family: inherit; /* Inherit from parent */
    font-size: 20px; /* Default font size for larger screens */
    font-weight: bold; /* Bold text */
    line-height: 1.2; /* Tightens vertical spacing */
    color: black; /* Default text color */
    padding: 5px 4px;
    clip-path: inset(0 100% 0 0);
    transition: color 0.4s ease, text-shadow 0.4s ease, transform 0.4s ease, clip-path 0.4s ease; /* Added transform transition */
  }
    
  /* Highlighted Word with Stroke and Black Text */
  .text-stroke-effect-5 span.highlighted {
    color: white; /* Change highlighted text color */
    text-shadow: 
      1px 1px 0 #FFFFFF,   /* White stroke to the bottom-right */
      -1px 1px 0 #FFFFFF,  /* White stroke to the bottom-left */
      1px -1px 0 #FFFFFF,  /* White stroke to the top-right */
      -1px -1px 0 #FFFFFF; /* White stroke to the top-left */
    clip-path: inset(0 0 0 0);
  }
  
  /* Responsive Styling for Mobile */
  @media (max-width: 768px) {
    .text-stroke-effect-5 span {
      font-size: 18px; /* Smaller font size for mobile screens */
      padding: 4px; /* Adjust padding for compact spacing */
    }
  }
</style>
