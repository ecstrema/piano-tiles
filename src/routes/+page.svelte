<script lang="ts">
  import Row from "./row.svelte";
import { persisted } from 'svelte-local-storage-store'
  import Time from "./time.svelte";

  import { onMount } from "svelte";
    import { get } from "svelte/store";

  let targets = createTargets();
  let nextTarget = 0;
  let hasLost = false;
  let hasWon = false;

  let startTimeStamp = 0;
  let time = 0;
  let stopTime = false;
  const bestTime = persisted("bestTime", Infinity);

  let errorLocation = {
    row: -1,
    column: -1,
  }

  function createTargets() {
    const rowsInLevel = 20;
    return new Array(rowsInLevel).fill(0).map(() => Math.floor(Math.random() * 4));
  }

  function restart() {
    startTimeStamp = 0;
    time = 0;
    stopTime = false;
    nextTarget = 0;
    hasLost = false;
    hasWon = false;
    errorLocation = {
      row: -1,
      column: -1,
    }
    targets = createTargets();
  }

  function startTime() {
    const updateTime: FrameRequestCallback = (ms) => {
      if (stopTime) {
        return;
      }
      if (!startTimeStamp) {
        startTimeStamp = ms;
      }
      time = ms - startTimeStamp
      window.requestAnimationFrame(updateTime);
    }
    updateTime(0);
  }

  onMount(() => {
    document.addEventListener("keydown", (ev) => {
      switch(ev.key) {
        case "a": onClick(nextTarget, 0); break;
        case "s": onClick(nextTarget, 1); break;
        case "d": onClick(nextTarget, 2); break;
        case "f": onClick(nextTarget, 3); break;
        case "\n":
        case " ": {
          if (hasLost || hasWon) {
            restart();
          }
          break;
        }
      }
    })
  })

  function onClick(row: number, column: number) {
    if (hasLost) {
      return;
    }
    if (time === 0) {
      startTime();
    }
    if (nextTarget === row && column === targets[row]) {
      nextTarget = nextTarget + 1
      if (nextTarget === targets.length) {
        hasWon = true;
        stopTime = true;
        const bt = get(bestTime);
        if (!bt || time < bt) {
          bestTime.set(time);
        }
      }
    }
    else {
      hasLost = true;
      stopTime = true;
      errorLocation = { row: row - nextTarget, column, }
    }
  }
</script>

<svg>
  {#each targets as target, rowIndex (rowIndex)}
    <Row row={rowIndex - nextTarget} targetNumber={target} validated={nextTarget > rowIndex} onClick={(column) => onClick((rowIndex), column)} errorLocation={errorLocation}/>
  {/each}
</svg>

<Time value={time} />

{#if hasLost}
<div style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; display: flex; flex-direction: column; align-items: center; justify-content: center; background-color: rgba(100, 100, 100, 0.6);">
  <button style="padding: 8px; border-radius: 8px; font-size: x-large;" on:click={restart}>
    Restart
  </button>
</div>
{/if}

{#if hasWon}
<div style="position: absolute; top: 0; left: 0; width: 100%; height: 100%; display: flex; flex-direction: column; align-items: center; justify-content: center; background-color: rgba(100, 100, 100, 0.6);">
  <h1 style="background-color: white; padding: 1em; border-radius: 1em;">Time: {(time / 1000).toFixed(2)}</h1>
  <h2 style="background-color: white; padding: 1em; border-radius: 1em;">Best time: {(get(bestTime) / 1000).toFixed(2)}</h2>
  <button style="padding: 8px; border-radius: 8px; font-size: x-large;" on:click={restart}>
    Restart
  </button>
</div>
{/if}
