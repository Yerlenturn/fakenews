<script lang="ts">
	import FlyIn from "$lib/FlyIn.svelte";
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
    ass
  </div>
</div>

<div class="flex h-screen w-screen self-center justify-center items-center scrollanim">
  ass
</div>

<style>
  
</style>

