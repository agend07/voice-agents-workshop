<script lang="ts">
  import { VoiceClient } from "@cloudflare/voice/client";
  import type { VoiceStatus, TranscriptMessage } from "@cloudflare/voice/client";
  import CallButton from "./components/CallButton.svelte";
  import StatusBadge from "./components/StatusBadge.svelte";
  import AudioLevelMeter from "./components/AudioLevelMeter.svelte";
  import MuteButton from "./components/MuteButton.svelte";
  import TranscriptView from "./components/TranscriptView.svelte";

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

  function toggleCall() {
    isActive ? client.endCall() : client.startCall();
  }
</script>

<div class="flex h-screen flex-col bg-neutral-50 text-neutral-900">
  <div class="mx-auto flex h-full w-full max-w-xl flex-col p-6 gap-6">
    <section class="flex flex-col items-center justify-center gap-5 rounded-2xl bg-white p-6">
      <div class="text-center">
        <h1 class="text-2xl font-semibold tracking-tight text-neutral-900">
          Cloudflare Voice Agent
        </h1>
        <p class="mt-1 text-sm text-neutral-500">
          Tap the phone button to start a call
        </p>
      </div>

      <CallButton active={isActive} onclick={toggleCall} />

      {#if isActive}
        <div class="grid w-full grid-cols-3 items-center gap-4 pt-2">
          <StatusBadge {status} />
          <AudioLevelMeter level={audioLevel} />
          <MuteButton muted={isMuted} onclick={() => client.toggleMute()} />
        </div>
      {/if}
    </section>

    <TranscriptView messages={transcript} interim={interimTranscript} />
  </div>
</div>
