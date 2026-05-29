<script lang="ts">
  import { steps } from "./steps";
  import BlockRenderer from "./blocks/BlockRenderer.svelte";
  import WorkshopHeader from "./WorkshopHeader.svelte";
  import WorkshopFooter from "./WorkshopFooter.svelte";

  let currentStep: number = $state((() => {
    const match = window.location.hash.match(/#\/workshop\/(\d+)/);
    return match ? Math.min(Number(match[1]), steps.length - 1) : 0;
  })());

  let step = $derived(steps[currentStep]);
  let progress = $derived(((currentStep + 1) / steps.length) * 100);

  function goTo(idx: number) {
    currentStep = Math.max(0, Math.min(idx, steps.length - 1));
    window.location.hash = `#/workshop/${currentStep}`;
  }

  function prev() { goTo(currentStep - 1); }
  function next() { goTo(currentStep + 1); }

  function onKeyDown(e: KeyboardEvent) {
    if (e.key === "ArrowRight" || e.key === "ArrowDown") {
      e.preventDefault();
      next();
    } else if (e.key === "ArrowLeft" || e.key === "ArrowUp") {
      e.preventDefault();
      prev();
    }
  }

  function onHashChange() {
    const match = window.location.hash.match(/#\/workshop\/(\d+)/);
    if (match) {
      currentStep = Math.min(Number(match[1]), steps.length - 1);
    }
  }
</script>

<svelte:window onkeydown={onKeyDown} onhashchange={onHashChange} />

<div class="h-dvh bg-white text-neutral-900 flex flex-col">
  <!-- Progress bar -->
  <div class="h-1 bg-neutral-100 w-full shrink-0">
    <div
      class="h-full bg-orange-500 transition-all duration-300"
      style="width: {progress}%"
    ></div>
  </div>

  <WorkshopHeader {currentStep} totalSteps={steps.length} />

  <main class="flex-1 overflow-y-auto min-h-0">
    <div class="max-w-3xl mx-auto px-6 py-10">
      <div class="mb-8">
        {#if step.subtitle}
          <p class="text-sm font-medium text-orange-500 mb-1 uppercase tracking-wider">
            {step.subtitle}
          </p>
        {/if}
        <h2 class="text-3xl font-bold text-neutral-900 tracking-tight">
          {step.title}
        </h2>
      </div>

      <div class="space-y-5">
        {#each step.blocks as block, i (currentStep + '-' + i)}
          <BlockRenderer {block} />
        {/each}
      </div>
    </div>
  </main>

  <WorkshopFooter {steps} {currentStep} onprev={prev} onnext={next} ongoto={goTo} />
</div>
