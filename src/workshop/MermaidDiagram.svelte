<script lang="ts">
  import { renderMermaidSVG } from "beautiful-mermaid";

  interface Props {
    code: string;
  }

  let { code }: Props = $props();

  let svg: string | null = $derived.by(() => {
    try {
      return renderMermaidSVG(code, {
        bg: "#ffffff",
        fg: "#27272a",
        accent: "#f97316",
        transparent: true,
      });
    } catch {
      return null;
    }
  });

  let error: string | null = $derived.by(() => {
    try {
      renderMermaidSVG(code, {
        bg: "#ffffff",
        fg: "#27272a",
        accent: "#f97316",
        transparent: true,
      });
      return null;
    } catch (err) {
      return err instanceof Error ? err.message : String(err);
    }
  });
</script>

{#if error}
  <pre class="text-sm text-red-600 bg-red-50 p-4 rounded-lg border border-red-200 overflow-x-auto">{error}</pre>
{:else if svg}
  <div
    class="overflow-x-auto rounded-lg border border-neutral-200 p-4 bg-white [&_svg]:max-w-full [&_svg]:h-auto"
  >{@html svg}</div>
{/if}
