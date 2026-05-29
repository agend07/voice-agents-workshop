<script lang="ts">
  interface Props {
    content: string;
  }

  let { content }: Props = $props();
</script>

<div class="border-l-4 border-amber-500 bg-amber-50 rounded-r-lg px-4 py-3 text-sm text-amber-800">
  <span class="font-semibold">Note: </span>
  {content}
</div>
