<script lang="ts">
  import { createHighlighter, type Highlighter } from "shiki";

  interface Props {
    code: string;
    language: string;
    filename?: string;
  }

  let { code, language, filename }: Props = $props();

  const LANGS = ["typescript", "tsx", "json", "html", "bash", "text"] as const;

  const highlighterPromise = createHighlighter({
    themes: ["github-light"],
    langs: [...LANGS],
  });

  let html: string = $state("");
  let copied: boolean = $state(false);

  $effect(() => {
    const lang = language === "jsonc" ? "json" : language;
    highlighterPromise.then((hl: Highlighter) => {
      const loadedLangs = hl.getLoadedLanguages();
      const safeLang = loadedLangs.includes(lang) ? lang : "text";
      html = hl.codeToHtml(code, { lang: safeLang, theme: "github-light" });
    });
  });

  function handleCopy() {
    navigator.clipboard.writeText(code).then(() => {
      copied = true;
      setTimeout(() => (copied = false), 2000);
    });
  }
</script>

<div class="relative group rounded-lg overflow-hidden text-sm border border-neutral-200">
  {#if filename}
    <div class="bg-neutral-100 text-neutral-500 px-4 py-2 text-xs font-mono border-b border-neutral-200">
      {filename}
    </div>
  {/if}
  <button
    onclick={handleCopy}
    class="absolute top-2 right-2 z-10 px-2 py-1 rounded text-xs bg-neutral-200 text-neutral-600 opacity-0 group-hover:opacity-100 transition-opacity hover:bg-neutral-300 cursor-pointer"
  >
    {copied ? "Copied!" : "Copy"}
  </button>
  {#if html}
    <div
      class="overflow-x-auto [&_pre]:!p-4 [&_pre]:!m-0 [&_pre]:!rounded-none [&_code]:!text-[13px] [&_code]:!leading-relaxed"
    >{@html html}</div>
  {:else}
    <pre class="bg-neutral-50 text-neutral-700 p-4 overflow-x-auto"><code>{code}</code></pre>
  {/if}
</div>
