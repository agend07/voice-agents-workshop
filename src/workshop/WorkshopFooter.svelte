<script lang="ts">
  import type { Step } from "./steps";

  interface Props {
    steps: Step[];
    currentStep: number;
    onprev: () => void;
    onnext: () => void;
    ongoto: (i: number) => void;
  }

  let { steps, currentStep, onprev, onnext, ongoto }: Props = $props();
</script>

<footer class="shrink-0 border-t border-neutral-200 bg-white px-6 py-4">
  <div class="max-w-3xl mx-auto flex items-center justify-between">
    <button
      onclick={onprev}
      disabled={currentStep === 0}
      class="px-4 py-2 rounded-lg text-sm font-medium bg-neutral-100 text-neutral-600 hover:bg-neutral-200 disabled:opacity-30 disabled:cursor-not-allowed transition-colors cursor-pointer"
    >
      &larr; Previous
    </button>

    <div class="hidden sm:flex items-center gap-1.5">
      {#each steps as _, i (i)}
        <button
          onclick={() => ongoto(i)}
          class="w-2 h-2 rounded-full transition-all cursor-pointer {i === currentStep ? 'bg-orange-500 scale-125' : i < currentStep ? 'bg-orange-300' : 'bg-neutral-200'}"
          title={steps[i].title}
        ></button>
      {/each}
    </div>

    <button
      onclick={onnext}
      disabled={currentStep === steps.length - 1}
      class="px-4 py-2 rounded-lg text-sm font-medium bg-orange-500 text-white hover:bg-orange-600 disabled:opacity-30 disabled:cursor-not-allowed transition-colors cursor-pointer"
    >
      Next &rarr;
    </button>
  </div>
</footer>
