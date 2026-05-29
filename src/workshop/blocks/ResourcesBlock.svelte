<script lang="ts">
  import { LINK_ICONS } from "../../icons";
  import QRCode from "../QRCode.svelte";

  interface Props {
    qrUrl: string;
    links: { label: string; url: string; icon?: string }[];
  }

  let { qrUrl, links }: Props = $props();
</script>

<div class="flex flex-col sm:flex-row gap-10 items-center sm:items-start">
  <div class="flex flex-col items-center gap-3 shrink-0">
    <QRCode url={qrUrl} size={180} />
    <p class="text-xs text-neutral-400">Scan for this page</p>
  </div>
  <div class="flex-1 space-y-2.5 w-full">
    {#each links as link, k (k)}
      {@const iconData = link.icon ? LINK_ICONS[link.icon] : null}
      <a
        href={link.url}
        target="_blank"
        rel="noopener noreferrer"
        class="flex items-center gap-3 px-4 py-2.5 rounded-lg border border-neutral-200 hover:border-orange-300 hover:bg-orange-50 transition-colors group"
      >
        {#if iconData}
          <svg class="w-4 h-4 text-neutral-400 group-hover:text-orange-500 shrink-0" viewBox={iconData.viewBox} fill="currentColor">
            <path d={iconData.path} />
          </svg>
        {/if}
        <span class="text-sm font-medium text-neutral-700 group-hover:text-orange-600">
          {link.label}
        </span>
        <span class="ml-auto text-xs text-neutral-300 group-hover:text-orange-400 truncate max-w-[200px] hidden sm:block">
          {link.url.replace(/^https?:\/\//, "")}
        </span>
      </a>
    {/each}
  </div>
</div>
