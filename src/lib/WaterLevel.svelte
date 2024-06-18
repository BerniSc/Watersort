<script>  
    import { slide } from "svelte/transition";

    export let color;  
</script>  
  
<div class="water-level" style="background-color: {color};" in:slide={{ duration: 1000 }}>

</div>  
  
<style>  
    .water-level {  
        height: 20%;                        /* Every Waterlevel should Take up 20% of the overall height */  
        width: 100%;  
        box-sizing: border-box;  
    }  
</style>  
