<script lang="ts">
  import qrcode from "qrcode-generator";

  interface Props {
    url: string;
    size?: number;
  }

  let { url, size = 200 }: Props = $props();

  let svgMarkup = $derived.by(() => {
    const qr = qrcode(0, "M");
    qr.addData(url);
    qr.make();

    const moduleCount = qr.getModuleCount();
    const cellSize = size / moduleCount;
    let paths = "";

    for (let row = 0; row < moduleCount; row++) {
      for (let col = 0; col < moduleCount; col++) {
        if (qr.isDark(row, col)) {
          const x = col * cellSize;
          const y = row * cellSize;
          paths += `M${x},${y}h${cellSize}v${cellSize}h-${cellSize}z`;
        }
      }
    }

    return `<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 ${size} ${size}" width="${size}" height="${size}"><path d="${paths}" fill="#18181b"/></svg>`;
  });
</script>

<div class="inline-block rounded-xl bg-white p-3 shadow-sm border border-neutral-200">
  {@html svgMarkup}
</div>
