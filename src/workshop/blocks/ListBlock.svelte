<script lang="ts">
  interface Props {
    items: string[];
  }

  let { items }: Props = $props();
</script>

<ul class="space-y-1.5 text-neutral-600">
  {#each items as item, j (j)}
    <li class="flex gap-2 leading-relaxed">
      <span class="text-orange-500 shrink-0 mt-1">&#x2022;</span>
      <span>{item}</span>
    </li>
  {/each}
</ul>
