<script lang="ts">
	import FlyIn from "$lib/FlyIn.svelte";
  import Test from "$lib/Test.svelte";
</script>

<div aria-label="outer-box" class="grid w-screen h-screen">
  <FlyIn class="h-3/6"
    animationLength={300} thresholdFactor={0} x={0} y={-150} opacity={0.7}>
    <div class="flex w-screen justify-center items-center"
      style="font-size: 50px; color: pink; transform: translateY(150px);"
    >
      wussup
    </div>
  </FlyIn>
  <div class="flex h-screen w-screen self-center justify-center items-center bg-black">
    <!-- <HighlightScroll> -->
    <!--   Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum -->
    <!-- </HighlightScroll> -->
    <Test/>
  </div>
</div>

<div class="flex h-screen w-screen self-center justify-center items-center scrollanim">
  ass
</div>

<style>
  
</style>

