<script>  
  import Bottle from './lib/Bottle.svelte';  
  export let gameState;  
</script>  
  
<div class="game-board">  
    {#each gameState.bottles as bottle, i (i)}  
        <div class="bottle">  
            <Bottle {bottle} id={i} />
        </div>
    {/each}  
</div>  

<style>
    .bottle {
        display: inline-block;
        margin: 10px;
    }
</style>