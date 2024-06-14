<script>  
  export let color;  
</script>  
  
<div class="water-level" style="background-color: {color};">

</div>  
  
<style>  
    .water-level {  
        height: 20%;                        /* Every Waterlevel should Take up 20% of the overall height */  
        width: 100%;  
        transition: background-color 0.3s;  
        box-sizing: border-box;  
    }  
</style>  
