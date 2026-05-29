<script lang="ts">
  import { renderMermaidSVG } from "beautiful-mermaid";

  interface Props {
    code: string;
  }

  let { code }: Props = $props();

  let result: { svg: string | null; error: string | null } = $derived.by(() => {
    try {
      const svg = renderMermaidSVG(code, {
        bg: "#ffffff",
        fg: "#27272a",
        accent: "#f97316",
        transparent: true,
      });
      return { svg, error: null };
    } catch (err) {
      return { svg: null, error: err instanceof Error ? err.message : String(err) };
    }
  });
</script>

{#if result.error}
  <pre class="text-sm text-red-600 bg-red-50 p-4 rounded-lg border border-red-200 overflow-x-auto">{result.error}</pre>
{:else if result.svg}
  <div
    class="overflow-x-auto rounded-lg border border-neutral-200 p-4 bg-white [&_svg]:max-w-full [&_svg]:h-auto"
  >{@html result.svg}</div>
{/if}
