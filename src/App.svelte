<script lang="ts">
  import { onMount } from "svelte";
  import TextCarpet from "./TextCarpet.svelte";

  let listLinks: Array<string> = [];
  onMount(async () => {
    const fetchResult = await fetch("./settings.json");
    const allDirs = await fetchResult.json();
    listLinks = allDirs["exposedDirectories"];
  });
</script>

<main>
  {#if listLinks.length > 0}
    <TextCarpet linkList={listLinks}></TextCarpet>
  {/if}
</main>

<style>
</style>
