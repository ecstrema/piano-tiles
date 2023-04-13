<h1><span>{(value / 1000).toFixed(2)}</span> sec.</h1>

<script lang="ts">
  /** Time in secs */
  export let value: number;
</script>

<style>
  h1 {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    padding: 1em;
    text-align: center;
    background-color: rgba(200, 200, 200, 0.8);
    margin: 0;
  }
  span {
    min-width: 60px;
  }
</style>
