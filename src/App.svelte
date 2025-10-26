<script lang="ts">
  import { onMount } from "svelte";
  import TextCarpet from "./TextCarpet.svelte";
  import Impressum from "./Impressum.svelte";

  let showImprint = $state(false);

  let listLinks: Array<string> = $state([]);
  onMount(async () => {
    const fetchResult = await fetch("./settings.json");
    const allDirs = await fetchResult.json();
    listLinks = allDirs["exposedDirectories"];
  });
</script>

<header>hallo</header>
<main>
  {#if listLinks.length > 0}
    <TextCarpet linkList={listLinks}></TextCarpet>
  {/if}
</main>
<footer>
  <div onclick={() => (showImprint = !showImprint)}>impressum</div>
  {#if showImprint}
    <Impressum></Impressum>
  {/if}
</footer>

<style>
  header {
    font-size: 4em;
  }

  main {
    margin: 0 2em;
    flex: 1;
    justify-content: center;
    display: flex;
    flex-direction: column;
  }

  footer {
    position: absolute;
    bottom: 0;
    right: 0;
    padding-right: 5px;
    padding-bottom: 2px;
    cursor: pointer;
    opacity: 0.5;
    font-size: 0.8em;
  }
</style>
