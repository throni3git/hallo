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

<header>hallo</header>
<main>
  {#if listLinks.length > 0}
    <TextCarpet linkList={listLinks}></TextCarpet>
  {/if}
</main>
<footer>impressum</footer>

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
