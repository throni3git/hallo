<script lang="ts">
  interface Props {
    linkList: Array<string>;
  }

  let { linkList }: Props = $props();

  let listSnippets: Array<string> = $derived.by(createSnippetsFromLinkList);
  let clientHeight = $state(0);
  let clientWidth = $state(0);

  function countLetters(input: string): Record<string, number> {
    const result: Record<string, number> = {};
    for (const char of input) {
      if (Object.hasOwn(result, char)) {
        result[char]++;
      } else {
        result[char] = 1;
      }
    }
    return result;
  }

  function getContinuousProbabilitySteps(letterMap: Record<string, number>) {
    const allKeys = Object.keys(letterMap);
    const allValues = Object.values(letterMap);
    const totalAmount = allValues.reduce((prev, curr) => prev + curr);
    let sum = 0;
    const cumulativePropabilities = allValues.map(
      ((sum = 0), (n) => (sum += n))
    );
    const propabilitiesInRange = cumulativePropabilities.map(
      (value) => value / totalAmount
    );
    const result: Record<string, number> = {};
    for (let idx = 0; idx < allValues.length; idx++) {
      result[allKeys[idx]] = propabilitiesInRange[idx];
    }
    return result;
  }

  function getRandomLetterFromProbabilities(
    letterMapWithContinuousPropabilities: Record<string, number>
  ) {
    const pick = Math.random();
    const allValues = Object.values(letterMapWithContinuousPropabilities);
    for (let idx = 0; idx < allValues.length; idx++) {
      const propabilityStep = allValues[idx];
      if (propabilityStep >= pick) {
        return Object.keys(letterMapWithContinuousPropabilities)[idx];
      }
    }
    // should never occur:
    return "";
  }

  function buildCreators(texts: Array<string>) {
    const allCreators = [];
    for (const text of texts) {
      const amounts = countLetters(text);
      const continousPropabilities = getContinuousProbabilitySteps(amounts);
      const creator = () => {
        return getRandomLetterFromProbabilities(continousPropabilities);
      };
      allCreators.push(creator);
    }
    return allCreators;
  }

  function buildCreatorPairs(creators: Array<() => string>) {
    const repeatedCreators = [
      creators[0],
      ...creators,
      creators[creators.length - 1],
    ];
    const allPairCreators = [];
    for (let idx = 0; idx < repeatedCreators.length - 1; idx++) {
      const pairCreator = (progress: number) => {
        const pick = Math.random();
        if (pick > progress) {
          const value1 = repeatedCreators[idx]();
          return value1;
        }
        const value2 = repeatedCreators[idx + 1]();
        return value2;
      };
      allPairCreators.push(pairCreator);
    }
    return allPairCreators;
  }

  function createSnippetsFromLinkList(): Array<string> {
    if (linkList.length === 0) {
      return [];
    }

    if (clientHeight === 0 || clientWidth === 0) {
      return [];
    }

    const creators = buildCreators(linkList);
    const pairCreators = buildCreatorPairs(creators);

    let snippetLength = clientWidth / linkList.length;
    snippetLength *= 2;
    snippetLength = Math.round(snippetLength);

    const result = [];
    for (const pairCreator of pairCreators) {
      const snippet = [];
      for (let idx = 0; idx < snippetLength; idx++) {
        const letter = pairCreator(idx / snippetLength);
        snippet.push(letter);
      }
      result.push(snippet.join(""));
    }
    return result;
  }
</script>

<div class="text-carpet" bind:clientHeight bind:clientWidth>
  {#if linkList.length > 0}
    {#each linkList as lll, idx}
      <span class="inbetween-text">{listSnippets[idx]}</span><a
        href={"./" + lll}>{lll}</a
      >{/each}<span class="inbetween-text">{listSnippets[linkList.length]}</span
    >
  {/if}
</div>

<style>
  .text-carpet {
    word-wrap: break-word;
  }

  .inbetween-text {
    opacity: 0.2;
  }
</style>
