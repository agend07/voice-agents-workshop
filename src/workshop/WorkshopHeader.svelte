<script lang="ts">
  import { GITHUB_ICON_PATH } from "../icons";

  interface Props {
    currentStep: number;
    totalSteps: number;
  }

  let { currentStep, totalSteps }: Props = $props();
</script>

<header class="shrink-0 border-b border-neutral-200 bg-white px-6 py-3 flex items-center justify-between">
  <a
    href="#/"
    class="text-sm text-neutral-400 hover:text-neutral-600 transition-colors"
  >
    &larr; Back to app
  </a>
  <div class="flex items-center gap-4">
    <a
      href="https://github.com/fayazara/voice-agents-workshop"
      target="_blank"
      rel="noopener noreferrer"
      class="flex items-center gap-1.5 text-sm text-neutral-400 hover:text-neutral-600 transition-colors"
    >
      <svg class="w-4 h-4" viewBox="0 0 16 16" fill="currentColor">
        <path d={GITHUB_ICON_PATH} />
      </svg>
      GitHub
    </a>
    <span class="text-sm text-neutral-400 font-mono">
      {currentStep + 1} / {totalSteps}
    </span>
  </div>
</header>
