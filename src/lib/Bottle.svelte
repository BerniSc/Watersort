<script>  
    import WaterLevel from './WaterLevel.svelte';  

    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();

    export let bottle;  
    
    export let selected = false;
    export let pouring = false; 
    
    export let id;
    export let height = 200;
    export let width = 50;
    
    // The bottle has been clicked
    function handleClick() {  
        console.log(`Bottle ${id} was clicked`, bottle);  
        dispatch('click');
    }  

    let className = '';
    export { className as class };
</script>  
  
<button class="bottle {className} {pouring ? 'pouring' : ''}" style="height: {height}px; width: {width}px;" on:click={handleClick} class:selected={selected}>  
    {#each bottle.levels as level, i (i)}  
        <WaterLevel color={level.color} />  
    {/each}  
    <div class="empty-space"></div>  
</button>  
  
<style>  
    .bottle {  
        display: flex;  
        flex-direction: column;                 /* Fill Water Vertically instead of Horizontally */
        border-radius: 25px 25px 5px 5px;       /* Display Bottom as flat and Top as Round, turn later for easier Animations and easier css*/  
        transform: rotate(180deg);              /* Turns the Glas for 180° to have the round piece displayed on the Bottom */
        background-color: transparent;          /* No Background */  
        border: 2px solid #000;               /* Display a Border around the Glass */  
        padding: 0;                             /* Ensure there is no space between the Glass and the Water */
        box-sizing: border-box;  
        overflow: hidden;                       /* Prevents the Overflow of the Water -> Adjusts to the round Bottom instead of overflowing it */  
        cursor: pointer;                        /* Display Cursor to signal Clickable Item */  
        transition: transform 0.3s;             /* Animation to display tilting */  
    }  
    
    .selected {  
        border: 3px solid blue; 
    }  
  
    .bottle:focus {  
        /* border: 4px solid red; */
    }  

    .pouring {  
        transform: translateY(2px); /* Style to be applied when a bottle is pouring */  
    }  
  
    .bottle:active {  
        /* transform: translateY(2px);         Style that is beeing applied on Click   */
    }  
  
    .empty-space {  
        height: 5%;                         /* Always have 5% space left at the Top */  
        width: 100%;  
        background-color: transparent;      /* Empty space should be Empty */  
    }  
</style>  
