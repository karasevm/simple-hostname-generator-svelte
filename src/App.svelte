<script lang="ts">
  import { Adjectives } from './lib/adjectives';
  import { Nouns } from './lib/nouns';
  import { cubicOut } from 'svelte/easing';

  const capitalize = (str: string): string => {
    return str.charAt(0).toUpperCase() + str.slice(1);
  };

  const generatePhraseArray = (count: number) => {
    const words: string[] = [];
    for (let i = 0; i < count; i++) {
      words.push(Adjectives[Math.floor(Math.random() * Adjectives.length)]);
    }
    words.push(Nouns[Math.floor(Math.random() * Adjectives.length)]);
    return words;
  };

  const formatPhraseArray = (phraseArray: string[], isCapitalized: boolean) => {
    let result = '';
    for (const word of phraseArray) {
      if (isCapitalized) {
        result += capitalize(word);
      } else {
        result += word;
      }
    }
    return result;
  };
  let capitalizedChecked = true;
  let count = 3;
  let phrase = generatePhraseArray(count);
  let formattedPhrase = formatPhraseArray(phrase, capitalizedChecked);
  let visible = true;

  const handlePhraseUpdate = (newPhrase = true) => {
    visible = false;
    if (newPhrase) {
      phrase = generatePhraseArray(count);
    }
  };

  const typewriter = (node: HTMLElement, { speed = 50 }) => {
    const valid =
      node.childNodes.length === 1 &&
      node.childNodes[0].nodeType === Node.TEXT_NODE;

    if (!valid) return {};

    const text = node.textContent;
    const duration = Math.floor(text.length * (50 / 10) + speed);
    console.log(duration);
    return {
      duration,
      delay: 0,
      easing: (t: number) => {
        const steps = text.length * 1.5 + 20;
        const delta = 1 / steps;
        return Math.round(t / delta) * delta;
      },
      css: (t: number) => `width: ${Math.round(t * 100)}%;`
    };
  };
</script>

{@debug count}
<main>
  <span
    >So you want {count + 1}
    {capitalizedChecked ? 'capitalized' : 'non-capitalized'} words? ok</span
  >
  {#if visible}
    <h2
      on:outroend={() => {
        formattedPhrase = formatPhraseArray(phrase, capitalizedChecked);
        visible = true;
      }}
      on:outrostart={() => {
        console.log('outrostart');
      }}
      in:typewriter={{ speed: 700 }}
      out:typewriter={{ speed: 300 }}
    >
      {formattedPhrase}
    </h2>
  {/if}
  <div class="inputs">
    <input
      on:change={() => {
        handlePhraseUpdate();
        console.log(count);
      }}
      type="range"
      bind:value={count}
      min="1"
      max="5"
    />
    <label>
      <input
        type="checkbox"
        on:change={() => {
          handlePhraseUpdate(false);
        }}
        bind:checked={capitalizedChecked}
      />
      Capitalize
    </label>
  </div>
  <button
    on:click={() => {
      handlePhraseUpdate();
    }}>Get new phrase</button
  >
</main>

<style>
  main {
    display: flex;
    flex-direction: column;
    max-width: 750px;
    align-self: center;
    width: 70vw;
  }
  @media all and (max-width: 750px) {
    main {
      width: 100vw;
    }
  }
  .inputs {
    display: flex;
    flex-direction: row;
  }
  .inputs * {
    flex-grow: 1;
  }
  label {
    margin-top: -4px;
    margin-left: auto;
    text-align: center;
  }
  input[type='range'] {
    padding: 0;
    align-self: center;
    width: 50%;
  }
  h2 {
    overflow: hidden;
    user-select: all;
    margin: 0 auto; /* Gives that scrolling effect as the typing happens */
    transition: all;
    margin-top: 10px;
    margin-bottom: 15px;
    /* font-size: 4.5vw; */
    /* word-wrap: break-word; */
  }
  span,
  h2 {
    text-align: center;
  }
</style>
