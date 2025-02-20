<script lang="ts">
  import { onMount } from 'svelte';
  /**
   * @typedef {object} FlyInProps
   * Properties for FlyIn scroll animation
   * @property {number} animationLength - pixel scroll length of animation
   * @property {number} thresholdFactor - start animation when reaching & of viewport ex. 80% = 0.8
   * @property {number} x - x axis pixel start offset 
   * @property {number} y - y axis pixel start offset
   * @property {number} opacity - starting opacity, default 1
   * @property {string} class - same as class defs for regular html elements
   */
  interface FlyInProps {
    // Scroll length of animation
    animationLength: number,
    // A desired threshold (e.g., start when element's top reaches % of viewport) ex. 80% = .8
    thresholdFactor: number,
    x: number,
    y: number,
    // 0-1 please
    opacity?: number,
    class?: string,
    children: any  
  }
  let scrollY = $state(0);
  let animatedEl: any = $state();
  // This will be our computed starting point for the animation
  let startScroll = $state(0);
  // Configurable scroll distance over which the animation will progress (in pixels)
  const { animationLength, thresholdFactor, x, y, opacity, children, ...rest }: FlyInProps = $props();
  // total scroll
  let totalScroll = $state(0);
  // pixel progress
  let position = $derived(scrollY - startScroll);

  let height = $state(0);
  let rtop = $state(0);

  // Reactive calculation: compute progress as a number between 0 and 1
  let progress = $derived(Math.min(Math.max(Math.max(position / (totalScroll - scrollY), position / animationLength), 0), 1));

  // Optional: drive some computed style based on progress (e.g., scale and opacity)
  // See what transforms ya can do https://developer.mozilla.org/en-US/docs/Web/CSS/transform
  let transformStyle = $derived(`translate(${x - (x*progress)}px, ${y - (y*progress)}px)`);

  const opacStart = 1 - (opacity || 1);
  let opacityStyle = $derived(opacity ? opacity + (opacStart * progress) : 1);

  // Compute startScroll based on the element's absolute position and desired threshold.
  // We calculate the absolute top position as (scrollY + element.getBoundingClientRect().top)
  // Then subtract (window.innerHeight * thresholdFactor) so that the animation starts when
  // the element's top reaches that percentage of the viewport.
  function computeStartScroll() {
    height = window.innerHeight;
    if (animatedEl) {
      const rect = animatedEl.getBoundingClientRect();
      rtop = rect.top;
      // If rect.top is negative or suspicious, fall back to offsetTop difference.
      let elementTop = rect.top;
      if (elementTop < 0) {
        // offsetTop gives the element’s distance from the document top.
        // To get its relative position, subtract the current scrollY.
        elementTop = animatedEl.offsetTop - scrollY;
      }
      startScroll = scrollY + elementTop - window.innerHeight * thresholdFactor;
    }
    totalScroll = document.documentElement.scrollHeight - window.innerHeight;
  }

  onMount(() => {
    // Compute the start scroll position on mount
    computeStartScroll();

    // Also update when window is resized (viewport size changes)
    const handleResize = () => computeStartScroll();
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  });

  $effect(() => console.log(progress));
</script>

<svelte:window bind:scrollY />

<div class={rest.class}
  bind:this={animatedEl}
  style="transform: {transformStyle}; opacity: {opacityStyle}; transition: transform 0.1s linear, opacity 0.1s linear"
>
  {@render children()}
</div>

