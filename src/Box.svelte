<script>
  import { onMount, createEventDispatcher } from "svelte";
  import { fade } from "svelte/transition";

  export let x;
  export let y;
  export let number;
  let colors = [
    "",
    "#1B4F72",
    "#27AE60",
    "#E74C3C",
    "#1C2833",
    "#943126",
    "#48C9B0",
    "#424949",
    "#45B39D"
  ];

  let marked = false;
  const dispatch = createEventDispatcher();

  function handleMouseClick(event) {
    dispatch("click", {
      x: x,
      y: y,
      type: number
    });
  }

  function handeRightClick(event) {
    event.preventDefault();
    marked = !marked;
  }

  onMount(async () => {});
</script>

<style>
  .box {
    display: flex;
    position: relative;
    width: 50px;
    height: 50px;
    transition: 0.1s ease-in-out;
    justify-content: center;
    align-items: center;
    border-radius: 4px;
    transform: scale(0.9);
    background-color: #ecf0f1;
    overflow: hidden;
    font-weight: 900;
    color: #2e4053;
    user-select: none;
  }
  .error {
    background-color: #ec7063;
  }
  .cover {
    position: absolute;
    justify-content: center;
    align-items: center;
    display: flex;
    width: 100%;
    height: 100%;
    background-color: #5499c7;
    transition: 0.1s ease-in-out;
    cursor: pointer;
  }
  .box:hover {
    transform: scale(1);
  }
  .box:hover .cover {
    background-color: #bdc3c7;
  }
</style>

<div
  class="box"
  class:error={number.clicked}
  on:click={handleMouseClick}
  on:contextmenu={handeRightClick}>
  {#if !number.visibility}
    <div out:fade={{ duration: 200 }} class="cover">
      {#if marked}
        <img src="assets/flag-icon.png" alt="mine" />
      {/if}
    </div>
  {/if}
  {#if number.number != -1}
    <div style="color: {colors[number.number]}">
      {number.number == 0 ? '' : number.number}
    </div>
  {:else}
    <img src="assets/mine-icon.png" alt="mine" />
  {/if}
</div>
