<script lang="ts">
  // Import either component (but not both in the same file to avoid confusion)
	import HighlightOnScroll from '$lib/HighlightOnScroll.svelte';
	import HighlightScroll from './HighlightScroll.svelte';
  
  // You can define reactive state using the new $ syntax in Svelte 5
  // For example, if you want to change colors dynamically:
  let highlightColor = $state('#ff3eff');
  let normalColor = $state('#333333');
  
  // Example of a function that could change the colors
  function changeColors() {
    highlightColor = '#00aaff';
    normalColor = '#222222';
  }
</script>

<style>
  section {
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  .content {
    max-width: 800px;
    margin: 0 auto;
    padding: 2rem;
    font-size: 1.5rem;
    line-height: 1.8;
  }
  
  h1, h2 {
    text-align: center;
  }
</style>

<div class="app">
  <!-- Intro section -->
  <section>
    <h1>Scroll down to see the text highlighting effect</h1>
  </section>
  
  <!-- Content with clip-path text highlight effect -->
  <div class="content">
    <h2>Clip-path Text Highlight</h2>
    <HighlightOnScroll normalColor={normalColor} highlightColor={highlightColor}>
      <p><span class="highlight-text">This text will change color as you scroll, with the color change moving from left to right.</span></p>
      <p>You can highlight <span class="highlight-text">specific phrases</span> or <span class="highlight-text">entire paragraphs</span> using this technique.</p>
      <p class="highlight-text">The highlighting effect is directly bound to your scroll position rather than using CSS transitions.</p>
      <p class="highlight-text">Only the text color changes, with no background highlight effect.</p>
    </HighlightOnScroll>
  </div>
  <HighlightScroll></HighlightScroll>
</div>  
