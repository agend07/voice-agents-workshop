<script lang="ts">
  interface Props {
    muted: boolean;
    onclick: () => void;
  }

  let { muted, onclick }: Props = $props();
</script>

<button
  {onclick}
  aria-label={muted ? "Unmute" : "Mute"}
  title={muted ? "Unmute" : "Mute"}
  class="flex h-8 w-8 items-center justify-center justify-self-end rounded-full bg-neutral-100 text-neutral-600 ring-1 ring-inset ring-neutral-200/70 transition-colors hover:bg-neutral-200"
>
  <img
    src={muted ? "https://api.iconify.design/lucide:mic-off.svg" : "https://api.iconify.design/lucide:mic.svg"}
    alt=""
    class="h-4 w-4"
  />
</button>
