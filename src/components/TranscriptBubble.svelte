<script lang="ts">
  interface Props {
    role: "user" | "assistant";
    text: string;
    interim?: boolean;
  }

  let { role, text, interim = false }: Props = $props();

  let alignment = $derived(role === "user" ? "justify-end" : "justify-start");
  let bubbleStyle = $derived(
    interim
      ? "bg-orange-500/60 text-white italic"
      : role === "user"
        ? "bg-orange-500 text-white"
        : "bg-neutral-200 text-neutral-800"
  );
</script>

<div class="flex {alignment}">
  <span class="max-w-[85%] rounded-2xl px-4 py-2 text-sm leading-relaxed {bubbleStyle}">
    {text}
  </span>
</div>
