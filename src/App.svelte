<script lang="ts">
  import VoiceApp from "./VoiceApp.svelte";
  import Workshop from "./workshop/Workshop.svelte";

  let route: "app" | "workshop" = $state(
    window.location.hash.startsWith("#/workshop") ? "workshop" : "app"
  );

  function onHashChange() {
    route = window.location.hash.startsWith("#/workshop") ? "workshop" : "app";
  }
</script>

<svelte:window onhashchange={onHashChange} />

{#if route === "workshop"}
  <Workshop />
{:else}
  <VoiceApp />
{/if}
