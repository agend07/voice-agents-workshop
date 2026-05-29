<script lang="ts">
  import type { VoiceStatus } from "@cloudflare/voice/client";

  interface Props {
    status: VoiceStatus;
  }

  let { status }: Props = $props();

  const LABELS: Record<VoiceStatus, string> = {
    idle: "Not connected",
    listening: "Listening",
    thinking: "Thinking",
    speaking: "Speaking",
  };

  const COLORS: Record<string, string> = {
    listening: "bg-green-500",
    thinking: "bg-yellow-500",
    speaking: "bg-blue-500",
  };

  let label = $derived(LABELS[status]);
  let dotColor = $derived(COLORS[status] ?? "bg-blue-500");
</script>

<div class="flex w-fit items-center gap-2 justify-self-start rounded-full bg-neutral-100/80 px-3 py-1.5 text-xs font-medium text-neutral-600 ring-1 ring-inset ring-neutral-200/70">
  <span class="relative flex size-2">
    <span class="absolute inline-flex h-full w-full rounded-full opacity-60 animate-ping {dotColor}"></span>
    <span class="relative inline-flex size-2 rounded-full {dotColor}"></span>
  </span>
  {label}
</div>
