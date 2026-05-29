<script lang="ts">
  import { steps } from "./steps";
  import CodeBlock from "./CodeBlock.svelte";
  import MermaidDiagram from "./MermaidDiagram.svelte";
  import QRCode from "./QRCode.svelte";
  import { LINK_ICONS, GITHUB_ICON_PATH } from "../icons";

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

  <!-- Header -->
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
        {currentStep + 1} / {steps.length}
      </span>
    </div>
  </header>

  <!-- Content area -->
  <main class="flex-1 overflow-y-auto min-h-0">
    <div class="max-w-3xl mx-auto px-6 py-10">
      <!-- Step title -->
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

      <!-- Blocks -->
      <div class="space-y-5">
        {#each step.blocks as block, i (currentStep + '-' + i)}
          {#if block.type === "text"}
            <p class="text-neutral-600 leading-relaxed">{block.content}</p>
          {:else if block.type === "heading"}
            <h3 class="text-lg font-semibold text-neutral-900 mt-2">{block.content}</h3>
          {:else if block.type === "code"}
            <CodeBlock code={block.content} language={block.language} filename={block.filename} />
          {:else if block.type === "diagram"}
            <MermaidDiagram code={block.content} />
          {:else if block.type === "note"}
            <div class="border-l-4 border-amber-500 bg-amber-50 rounded-r-lg px-4 py-3 text-sm text-amber-800">
              <span class="font-semibold">Note: </span>
              {block.content}
            </div>
          {:else if block.type === "list"}
            <ul class="space-y-1.5 text-neutral-600">
              {#each block.items as item, j (j)}
                <li class="flex gap-2 leading-relaxed">
                  <span class="text-orange-500 shrink-0 mt-1">&#x2022;</span>
                  <span>{item}</span>
                </li>
              {/each}
            </ul>
          {:else if block.type === "intro"}
            <div class="flex flex-col items-center text-center gap-5 py-6">
              <img
                src={block.photo}
                alt={block.name}
                class="w-28 h-28 rounded-full ring-4 ring-orange-100 object-cover"
              />
              <div>
                <h3 class="text-2xl font-bold text-neutral-900">{block.name}</h3>
                <p class="text-neutral-500 mt-1">{block.role}</p>
              </div>
            </div>
          {:else if block.type === "resources"}
            <div class="flex flex-col sm:flex-row gap-10 items-center sm:items-start">
              <div class="flex flex-col items-center gap-3 shrink-0">
                <QRCode url={block.qrUrl} size={180} />
                <p class="text-xs text-neutral-400">Scan for this page</p>
              </div>
              <div class="flex-1 space-y-2.5 w-full">
                {#each block.links as link, k (k)}
                  {@const iconData = link.icon ? LINK_ICONS[link.icon] : null}
                  <a
                    href={link.url}
                    target="_blank"
                    rel="noopener noreferrer"
                    class="flex items-center gap-3 px-4 py-2.5 rounded-lg border border-neutral-200 hover:border-orange-300 hover:bg-orange-50 transition-colors group"
                  >
                    {#if iconData}
                      <svg class="w-4 h-4 text-neutral-400 group-hover:text-orange-500 shrink-0" viewBox={iconData.viewBox} fill="currentColor">
                        <path d={iconData.path} />
                      </svg>
                    {/if}
                    <span class="text-sm font-medium text-neutral-700 group-hover:text-orange-600">
                      {link.label}
                    </span>
                    <span class="ml-auto text-xs text-neutral-300 group-hover:text-orange-400 truncate max-w-[200px] hidden sm:block">
                      {link.url.replace(/^https?:\/\//, "")}
                    </span>
                  </a>
                {/each}
              </div>
            </div>
          {/if}
        {/each}
      </div>
    </div>
  </main>

  <!-- Navigation footer -->
  <footer class="shrink-0 border-t border-neutral-200 bg-white px-6 py-4">
    <div class="max-w-3xl mx-auto flex items-center justify-between">
      <button
        onclick={prev}
        disabled={currentStep === 0}
        class="px-4 py-2 rounded-lg text-sm font-medium bg-neutral-100 text-neutral-600 hover:bg-neutral-200 disabled:opacity-30 disabled:cursor-not-allowed transition-colors cursor-pointer"
      >
        &larr; Previous
      </button>

      <!-- Step dots -->
      <div class="hidden sm:flex items-center gap-1.5">
        {#each steps as _, i (i)}
          <button
            onclick={() => goTo(i)}
            class="w-2 h-2 rounded-full transition-all cursor-pointer {i === currentStep ? 'bg-orange-500 scale-125' : i < currentStep ? 'bg-orange-300' : 'bg-neutral-200'}"
            title={steps[i].title}
          ></button>
        {/each}
      </div>

      <button
        onclick={next}
        disabled={currentStep === steps.length - 1}
        class="px-4 py-2 rounded-lg text-sm font-medium bg-orange-500 text-white hover:bg-orange-600 disabled:opacity-30 disabled:cursor-not-allowed transition-colors cursor-pointer"
      >
        Next &rarr;
      </button>
    </div>
  </footer>
</div>
