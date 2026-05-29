<script lang="ts">
  import type { ContentBlock } from "../steps";
  import CodeBlock from "../CodeBlock.svelte";
  import MermaidDiagram from "../MermaidDiagram.svelte";
  import NoteBlock from "./NoteBlock.svelte";
  import ListBlock from "./ListBlock.svelte";
  import IntroBlock from "./IntroBlock.svelte";
  import ResourcesBlock from "./ResourcesBlock.svelte";

  interface Props {
    block: ContentBlock;
  }

  let { block }: Props = $props();
</script>

{#if block.type === "text"}
  <p class="text-neutral-600 leading-relaxed">{block.content}</p>
{:else if block.type === "heading"}
  <h3 class="text-lg font-semibold text-neutral-900 mt-2">{block.content}</h3>
{:else if block.type === "code"}
  <CodeBlock code={block.content} language={block.language} filename={block.filename} />
{:else if block.type === "diagram"}
  <MermaidDiagram code={block.content} />
{:else if block.type === "note"}
  <NoteBlock content={block.content} />
{:else if block.type === "list"}
  <ListBlock items={block.items} />
{:else if block.type === "intro"}
  <IntroBlock name={block.name} role={block.role} photo={block.photo} />
{:else if block.type === "resources"}
  <ResourcesBlock qrUrl={block.qrUrl} links={block.links} />
{/if}
