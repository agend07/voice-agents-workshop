<script lang="ts">
  import { PHONE_ICON_PATH, PHONE_HANGUP_ICON_PATH } from "../icons";

  interface Props {
    active: boolean;
    onclick: () => void;
  }

  let { active, onclick }: Props = $props();
</script>

<button
  {onclick}
  aria-label={active ? "End call" : "Start call"}
  title={active ? "End call" : "Start call"}
  class="flex size-14 items-center justify-center rounded-full text-3xl text-white shadow-md transition-all cursor-pointer {active ? 'bg-red-500 hover:bg-red-600' : 'bg-green-500 hover:bg-green-600'}"
>
  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" class="size-5">
    <path fill="currentColor" d={active ? PHONE_HANGUP_ICON_PATH : PHONE_ICON_PATH} />
  </svg>
</button>
