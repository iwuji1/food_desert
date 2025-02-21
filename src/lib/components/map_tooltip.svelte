<script>
    export let data;
    export let mapx;
    export let mapy;

    let width = window.innerWidth

    import { fly, fade } from "svelte/transition"

    let tooltipWidth;

    const xNudge = 2;
    const yNudge = 2;

    $: xPosition = mapx + tooltipWidth + xNudge > width ? mapx - tooltipWidth - xNudge : mapx + xNudge;
    $: yPosition = mapy + yNudge
</script>

<style>
    .tooltip {
        position:absolute;
        background: white;
        box-shadow: rgba(0, 0, 0, 0.15) 2px 3px 8px; /* Gives a nice 3d effect */
        padding: 8px 6px; /* Adds space around content within tooltip */
        border-radius: 3px; /* Rounds corners */
        pointer-events: none;
        z-index: 10;
        transition: top 300ms ease, left 300ms ease;
    }

    h1 {
      margin: 0;
      font-size: 1rem;
      font-weight: 500;
      margin-bottom: 3px;
      font-family: sans-serif;
  }

    
</style>

<div 
class="tooltip"
in:fly={{ y: 10, duration: 200, delay: 200 }}
out:fade
style="left:{xPosition}px; top:{yPosition}px;"
bind:clientWidth={tooltipWidth}>
  <h1>{data.Store}</h1>
</div>