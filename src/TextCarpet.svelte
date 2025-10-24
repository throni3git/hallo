<script lang="ts">
  let { links } = $props();

  const listLinks = links;
  const filteredLinks = listLinks;

  function countLetters(input: string): Record<string, number> {
    const result = {};
    for (const char of input) {
      if (Object.hasOwn(result, char)) {
        result[char]++;
      } else {
        result[char] = 1;
      }
    }
    return result;
  }

  function getContinuousProbabilitySteps(letterMap) {
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
    const result = {};
    for (let idx = 0; idx < allValues.length; idx++) {
      result[allKeys[idx]] = propabilitiesInRange[idx];
    }
    return result;
  }

  function getRandomLetterFromProbabilities(
    letterMapWithContinuousPropabilities
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

  function buildCreators(texts) {
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

  function buildCreatorPairs(creators) {
    const repeatedCreators = [
      creators[0],
      ...creators,
      creators[creators.length - 1],
    ];
    const allPairCreators = [];
    for (let idx = 0; idx < repeatedCreators.length - 1; idx++) {
      const pairCreator = (progress) => {
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

  const listSnippets: Array<string> = [];
  if (filteredLinks.length > 0) {
    const creators = buildCreators(filteredLinks);
    const pairCreators = buildCreatorPairs(creators);

    const SNIPPET_LENGTH = 1000;

    for (const pairCreator of pairCreators) {
      const snippet = [];
      for (let idx = 0; idx < SNIPPET_LENGTH; idx++) {
        const letter = pairCreator(idx / SNIPPET_LENGTH);
        snippet.push(letter);
      }
      listSnippets.push(snippet.join(""));
    }

    const listFragments = [];
    for (let idx = 0; idx < filteredLinks.length; idx++) {
      const spanElement = document.createElement("span");
      spanElement.classList.add("inbetween-text");
      spanElement.innerText = listSnippets[idx];
      listFragments.push(spanElement);

      const linkElement = document.createElement("a");
      linkElement.href = "./" + filteredLinks[idx];
      linkElement.innerText = filteredLinks[idx];
      listFragments.push(linkElement);
    }
    const spanElement = document.createElement("span");
    spanElement.classList.add("inbetween-text");
    spanElement.innerText = listSnippets[listSnippets.length - 1];
    listFragments.push(spanElement);

    console.log(listFragments);
  }
</script>

<div class="text-carpet">
  {#if filteredLinks.length > 0}
    {#each filteredLinks as lll, idx}
      <span class="inbetween-text">{listSnippets[idx]}</span><a
        href={"./" + lll}>{lll}</a
      >{/each}<span class="inbetween-text"
      >{listSnippets[filteredLinks.length]}</span
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
