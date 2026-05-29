<script lang="ts">
  interface Props {
    level: number;
  }

  let { level }: Props = $props();

  const BAR_COUNT = 5;

  function isActive(index: number): boolean {
    return level > ((index + 1) / BAR_COUNT) * 0.5;
  }

  function height(index: number): string {
    return isActive(index) ? `${Math.max(6, level * 24)}px` : "6px";
  }

  function color(index: number): string {
    return isActive(index) ? "rgb(34 197 94)" : "rgb(212 212 212)";
  }
</script>

<div class="flex h-6 items-end justify-self-center gap-1">
  {#each Array(BAR_COUNT) as _, i}
    <div
      class="w-1.5 rounded-full transition-all duration-150"
      style="height: {height(i)}; background-color: {color(i)};"
    ></div>
  {/each}
</div>
