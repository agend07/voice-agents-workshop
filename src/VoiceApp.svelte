<script lang="ts">
  import { VoiceClient } from "@cloudflare/voice/client";
  import type { VoiceStatus, TranscriptMessage } from "@cloudflare/voice/client";
  import { PHONE_ICON_PATH, PHONE_HANGUP_ICON_PATH } from "./icons";

  /** Status label dictionary — replaces nested ternary chains */
  const STATUS_LABELS: Record<VoiceStatus, string> = {
    idle: "Not connected",
    listening: "Listening",
    thinking: "Thinking",
    speaking: "Speaking",
  };

  /** Status dot color classes */
  const STATUS_COLORS: Record<Exclude<VoiceStatus, "idle">, string> = {
    listening: "bg-green-500",
    thinking: "bg-yellow-500",
    speaking: "bg-blue-500",
  };

  let status: VoiceStatus = $state("idle");
  let transcript: TranscriptMessage[] = $state([]);
  let interimTranscript: string | null = $state(null);
  let audioLevel: number = $state(0);
  let isMuted: boolean = $state(false);

  const client = new VoiceClient({ agent: "VoiceAgent" });

  client.addEventListener("statuschange", (s) => (status = s));
  client.addEventListener("transcriptchange", (t) => (transcript = t));
  client.addEventListener("interimtranscript", (t) => (interimTranscript = t));
  client.addEventListener("audiolevelchange", (l) => (audioLevel = l));
  client.addEventListener("mutechange", (m) => (isMuted = m));

  let isActive = $derived(status !== "idle");
  let statusLabel = $derived(STATUS_LABELS[status]);
  let dotColor = $derived(STATUS_COLORS[status as Exclude<VoiceStatus, "idle">] ?? "bg-blue-500");

  let scrollEnd: HTMLDivElement;

  $effect(() => {
    // Re-run whenever transcript or interim changes
    transcript;
    interimTranscript;
    scrollEnd?.scrollIntoView({ behavior: "smooth", block: "end" });
  });

  function toggleCall() {
    if (isActive) {
      client.endCall();
    } else {
      client.startCall();
    }
  }

  function toggleMute() {
    client.toggleMute();
  }

  /** Audio level bar helpers */
  const BAR_COUNT = 5;
  function barActive(index: number): boolean {
    return audioLevel > ((index + 1) / BAR_COUNT) * 0.5;
  }
  function barHeight(index: number): string {
    return barActive(index) ? `${Math.max(6, audioLevel * 24)}px` : "6px";
  }
  function barColor(index: number): string {
    return barActive(index) ? "rgb(34 197 94)" : "rgb(212 212 212)";
  }
</script>

<div class="flex h-screen flex-col bg-neutral-50 text-neutral-900">
  <div class="mx-auto flex h-full w-full max-w-xl flex-col p-6 gap-6">
    <!-- Top half - title + call button -->
    <section class="flex flex-col items-center justify-center gap-5 rounded-2xl bg-white p-6">
      <div class="text-center">
        <h1 class="text-2xl font-semibold tracking-tight text-neutral-900">
          Cloudflare Voice Agent
        </h1>
        <p class="mt-1 text-sm text-neutral-500">
          Tap the phone button to start a call
        </p>
      </div>

      <button
        onclick={toggleCall}
        aria-label={isActive ? "End call" : "Start call"}
        title={isActive ? "End call" : "Start call"}
        class="flex size-14 items-center justify-center rounded-full text-3xl text-white shadow-md transition-all cursor-pointer {isActive ? 'bg-red-500 hover:bg-red-600' : 'bg-green-500 hover:bg-green-600'}"
      >
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" class="size-5">
          <path fill="currentColor" d={isActive ? PHONE_HANGUP_ICON_PATH : PHONE_ICON_PATH} />
        </svg>
      </button>

      {#if isActive}
        <div class="grid w-full grid-cols-3 items-center gap-4 pt-2">
          <!-- Status badge -->
          <div class="flex w-fit items-center gap-2 justify-self-start rounded-full bg-neutral-100/80 px-3 py-1.5 text-xs font-medium text-neutral-600 ring-1 ring-inset ring-neutral-200/70">
            <span class="relative flex size-2">
              <span class="absolute inline-flex h-full w-full rounded-full opacity-60 animate-ping {dotColor}"></span>
              <span class="relative inline-flex size-2 rounded-full {dotColor}"></span>
            </span>
            {statusLabel}
          </div>

          <!-- Audio level -->
          <div class="flex h-6 items-end justify-self-center gap-1">
            {#each Array(BAR_COUNT) as _, i}
              <div
                class="w-1.5 rounded-full transition-all duration-150"
                style="height: {barHeight(i)}; background-color: {barColor(i)};"
              ></div>
            {/each}
          </div>

          <!-- Mute button -->
          <button
            onclick={toggleMute}
            aria-label={isMuted ? "Unmute" : "Mute"}
            title={isMuted ? "Unmute" : "Mute"}
            class="flex h-8 w-8 items-center justify-center justify-self-end rounded-full bg-neutral-100 text-neutral-600 ring-1 ring-inset ring-neutral-200/70 transition-colors hover:bg-neutral-200"
          >
            <img
              src={isMuted ? "https://api.iconify.design/lucide:mic-off.svg" : "https://api.iconify.design/lucide:mic.svg"}
              alt=""
              class="h-4 w-4"
            />
          </button>
        </div>
      {/if}
    </section>

    <!-- Bottom half - transcript -->
    <section class="flex flex-1 min-h-0 flex-col overflow-hidden pb-6">
      <div
        class="flex-1 space-y-3 overflow-y-auto py-2 [scrollbar-width:none] [&::-webkit-scrollbar]:hidden"
        style="ms-overflow-style: none;"
      >
        {#if transcript.length === 0 && !interimTranscript}
          <p class="pt-6 text-center text-sm text-neutral-400">
            Your conversation will appear here.
          </p>
        {/if}

        {#each transcript as msg, i (i)}
          <div class="flex {msg.role === 'user' ? 'justify-end' : 'justify-start'}">
            <span class="max-w-[85%] rounded-2xl px-4 py-2 text-sm leading-relaxed {msg.role === 'user' ? 'bg-orange-500 text-white' : 'bg-neutral-200 text-neutral-800'}">
              {msg.text}
            </span>
          </div>
        {/each}

        {#if interimTranscript}
          <div class="flex justify-end">
            <span class="max-w-[85%] rounded-2xl bg-orange-500/60 px-4 py-2 text-sm italic text-white">
              {interimTranscript}
            </span>
          </div>
        {/if}

        <div bind:this={scrollEnd}></div>
      </div>
    </section>
  </div>
</div>
