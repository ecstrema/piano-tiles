{#each [0, 1, 2, 3] as column (column)}
  <!-- svelte-ignore a11y-click-events-have-key-events -->
  <rect
    class:target={targetNumber===column}
    class:validated={validated}
    class:error={errorLocation.column === column && errorLocation.row === row}
    on:click={() => onClick(column)}
    y={`${75 - row * 25}%`}
    x={`${column * 25}%`}
  />
{/each}

<script lang="ts">
  export let row: number;
  /**
   * Entre 0 et 3, le numero de la colonne à colorer
   */
  export let targetNumber: number;

  export let validated: boolean;

  export let onClick: (target: number) => void;

  export let errorLocation: {row: number, column: number};
</script>

<style>
  rect {
    width: 25%;
    height: 25%;
    fill: white;
    stroke-width: 2px;
    stroke: black;
    transition: y 80ms linear;
  }
  .target {
    fill: black;
  }
  /* .target:hover {
    fill: red;
  } */
  .target.validated {
    fill: rgb(100, 100, 100);
  }

  .error {
    fill: red;
    animation: color-bounce 1s infinite;
  }

@keyframes color-bounce {
  0% {
    fill: white;
  }
  50% {
    fill: red;
  }
  100% {
    fill: white;
  }
}
</style>
