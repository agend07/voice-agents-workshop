<script lang="ts">
  import type { TranscriptMessage } from "@cloudflare/voice/client";
  import TranscriptBubble from "./TranscriptBubble.svelte";

  interface Props {
    messages: TranscriptMessage[];
    interim: string | null;
  }

  let { messages, interim }: Props = $props();

  let scrollEnd: HTMLDivElement;

  $effect(() => {
    messages;
    interim;
    scrollEnd?.scrollIntoView({ behavior: "smooth", block: "end" });
  });
</script>

<section class="flex flex-1 min-h-0 flex-col overflow-hidden pb-6">
  <div
    class="flex-1 space-y-3 overflow-y-auto py-2 [scrollbar-width:none] [&::-webkit-scrollbar]:hidden"
    style="ms-overflow-style: none;"
  >
    {#if messages.length === 0 && !interim}
      <p class="pt-6 text-center text-sm text-neutral-400">
        Your conversation will appear here.
      </p>
    {/if}

    {#each messages as msg, i (i)}
      <TranscriptBubble role={msg.role} text={msg.text} />
    {/each}

    {#if interim}
      <TranscriptBubble role="user" text={interim} interim />
    {/if}

    <div bind:this={scrollEnd}></div>
  </div>
</section>
