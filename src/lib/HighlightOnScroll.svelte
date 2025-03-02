<script lang="ts">
  import { onMount, onDestroy } from 'svelte';
  
  // Props with default values and types
  export let startScrollPosition: number = 0.4; // When to start the animation (0-1 of viewport height)
  export let endScrollPosition: number = 0.8;   // When to end the animation (0-1 of viewport height)
  export let normalColor: string = '#333333';   // Original text color
  export let highlightColor: string = '#ff3e00'; // Highlighted text color
  export let easingFunction: string = 'easeInOutQuad'; // Type of easing to use
  
  type TextElement = HTMLElement;
  
  let container: HTMLElement;
  let textElements: TextElement[] = [];
  
  // Define the scroll listener function with proper type
  let scrollListener: (() => void) | null = null;
  
  // Collection of easing functions
  const easings = {
    // No easing, linear
    linear: (t: number): number => t,
    
    // Quadratic easing
    easeInQuad: (t: number): number => t * t,
    easeOutQuad: (t: number): number => t * (2 - t),
    easeInOutQuad: (t: number): number => t < 0.5 ? 2 * t * t : -1 + (4 - 2 * t) * t,
    
    // Cubic easing
    easeInCubic: (t: number): number => t * t * t,
    easeOutCubic: (t: number): number => (--t) * t * t + 1,
    easeInOutCubic: (t: number): number => t < 0.5 ? 4 * t * t * t : (t - 1) * (2 * t - 2) * (2 * t - 2) + 1,
    
    // Sine easing
    easeInSine: (t: number): number => 1 - Math.cos(t * Math.PI / 2),
    easeOutSine: (t: number): number => Math.sin(t * Math.PI / 2),
    easeInOutSine: (t: number): number => -(Math.cos(Math.PI * t) - 1) / 2,
    
    // Exponential easing
    easeInExpo: (t: number): number => t === 0 ? 0 : Math.pow(2, 10 * t - 10),
    easeOutExpo: (t: number): number => t === 1 ? 1 : 1 - Math.pow(2, -10 * t),
    easeInOutExpo: (t: number): number => t === 0 ? 0 : t === 1 ? 1 : t < 0.5 ? Math.pow(2, 20 * t - 10) / 2 : (2 - Math.pow(2, -20 * t + 10)) / 2,
    
    // Elastic easing
    easeOutElastic: (t: number): number => {
      const c4 = (2 * Math.PI) / 3;
      return t === 0 ? 0 : t === 1 ? 1 : Math.pow(2, -10 * t) * Math.sin((t * 10 - 0.75) * c4) + 1;
    }
  };
  
  // Get the selected easing function
  const getEasing = (name: string): ((t: number) => number) => {
    return easings[name as keyof typeof easings] || easings.linear;
  };
  
  function setupHighlightElements() {
    // Get all elements to be highlighted
    textElements = Array.from(container.querySelectorAll('.highlight-text'));
    
    // Set up the initial styling for all text elements
    textElements.forEach(element => {
      const textContent = element.textContent || '';
      element.innerHTML = '';
      element.style.position = 'relative';
      element.style.overflow = 'hidden'; // Ensure the overflow is hidden
      
      // Create the original text (will be clipped)
      const originalText = document.createElement('span');
      originalText.textContent = textContent;
      originalText.style.color = normalColor;
      originalText.style.position = 'relative';
      originalText.style.zIndex = '1';
      originalText.classList.add('original-text');
      
      // Create the highlighted version (will be revealed progressively)
      const highlightedText = document.createElement('span');
      highlightedText.textContent = textContent;
      highlightedText.style.color = highlightColor;
      highlightedText.style.position = 'absolute';
      highlightedText.style.top = '0';
      highlightedText.style.left = '0';
      highlightedText.style.zIndex = '2';
      highlightedText.style.clipPath = 'inset(0 100% 0 0)'; // Clip everything initially
      highlightedText.classList.add('highlighted-text');
      
      element.appendChild(originalText);
      element.appendChild(highlightedText);
    });
  }
  
  function updateHighlight() {
    const windowHeight = window.innerHeight;
    const scrollTop = window.scrollY;
    const ease = getEasing(easingFunction);
    
    textElements.forEach(element => {
      const rect = element.getBoundingClientRect();
      const elementTop = rect.top + scrollTop;
      
      // Calculate when this element should start and end highlighting
      const elementStartPosition = elementTop - windowHeight * endScrollPosition;
      const elementEndPosition = elementTop - windowHeight * startScrollPosition;
      
      // Calculate linear progress (0 to 1)
      let linearProgress = (scrollTop - elementStartPosition) / (elementEndPosition - elementStartPosition);
      linearProgress = Math.min(Math.max(linearProgress, 0), 1); // Clamp between 0 and 1
      
      // Apply easing function to get non-linear progress
      const easedProgress = ease(linearProgress);
      
      // Apply the clip-path based on eased progress
      const highlightedText = element.querySelector('.highlighted-text') as HTMLElement | null;
      if (highlightedText) {
        highlightedText.style.clipPath = `inset(0 ${100 - (easedProgress * 100)}% 0 0)`;
      }
    });
  }
  
  onMount(() => {
    setupHighlightElements();
    
    // Add scroll event listener
    scrollListener = () => {
      window.requestAnimationFrame(updateHighlight);
    };
    
    window.addEventListener('scroll', scrollListener);
    
    // Run once on mount to set initial state
    updateHighlight();
  });
  
  onDestroy(() => {
    // Clean up event listener
    if (scrollListener) {
      window.removeEventListener('scroll', scrollListener);
    }
  });
</script>

<style>
  .scroll-highlight-container {
    /* Container styling if needed */
  }
  
  :global(.highlight-text) {
    position: relative;
    display: inline-block;
  }
</style>

<div class="scroll-highlight-container" bind:this={container}>
  <slot></slot>
</div>
