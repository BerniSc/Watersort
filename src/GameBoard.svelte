<script>  
    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();

    import Bottle from './lib/Bottle.svelte';  
    
    import { strGlassNumber } from './stores/state';

    export let gameState;  

    const spacePerBottle = 5; // Assume each bottle has a capacity of 5 levels

    // Store the selected bottle index 
    let selectedBottle = null;  
    let pouringBottle = null;  

    let finishedBottles;
    $: finishedBottles = Array($strGlassNumber).fill(false);

    $: {
        console.log("Finished Bottles", finishedBottles);
        if(finishedBottles.filter(b => b).length === $strGlassNumber) {
            console.log("All Bottles are finished, Dispatching so");
            dispatch('winCurrentBoard');
        }
    }


  
    // Either choose a bottle or pour water from one bottle to another
    function selectOrPour(bottleIndex) {  
        if (selectedBottle === null) {  
            // No bottle is currently selected, so choose this on
            selectedBottle = bottleIndex;  
        } else {  
            // Another bottle has been selected, so we TRY to pour Water from this one to the other
            // TODO add Error Handling

            if(!pourWater(selectedBottle, bottleIndex)) {
                selectedBottle = null;                      // Reset the Selection
                return;
            }

            pouringBottle = selectedBottle; 
            setTimeout(() => {  
                pouringBottle = null;  
            }, 300);                                    // Has to match the transition time of the Bottle -> TODO make shared Value

            selectedBottle = null;                      // Reset the Selection
        }  
    }  

    // Add the logic to Pour Water from one Bottle to the Other
    function pourWater(fromBottleIndex, toBottleIndex) {  
        // Get our Bottles from the JSON Object
        const fromBottle = gameState.bottles[fromBottleIndex];  
        const toBottle = gameState.bottles[toBottleIndex];  
    
        // Get the Top Level Color from the _'from Bottle'_
        const fromTopLevelColor = fromBottle.levels.length > 0 ? fromBottle.levels[fromBottle.levels.length - 1].color : null;  
    
        // Get the Top Level Color from the _'to Bottle'_
        const toTopLevelColor = toBottle.levels.length > 0 ? toBottle.levels[toBottle.levels.length - 1].color : null;  
    
        // Test if the Bottles are compatible (Same Color or Empty Bottle)
        if(fromTopLevelColor && (toTopLevelColor === fromTopLevelColor || toBottle.levels.length === 0)) {  
            
            const availableSpace = spacePerBottle - toBottle.levels.length;
        
            // Calculate how many levels can be poured
            let levelsToPour = 0;  
            for(let i = fromBottle.levels.length - 1; i >= 0; i--) {  
                if(fromBottle.levels[i].color === fromTopLevelColor)  
                    levelsToPour++;  
                else  
                    break;  
            }  
        
            // Just Pour as many levels as possible
            levelsToPour = Math.min(levelsToPour, availableSpace);  
        
            if(levelsToPour === 0)  
                return false;

            // Refresh the State (Pour)
            if(levelsToPour > 0) {  
                // Remove the poured levels from the 'fromBottle'
                const pouredLevels = fromBottle.levels.splice(fromBottle.levels.length - levelsToPour, levelsToPour);  
        
                // Add the poured levels to the 'toBottle'
                toBottle.levels = toBottle.levels.concat(pouredLevels);  
        
                // Update the State
                gameState = { ...gameState };           // Refresh the State per assignment

                return true;
            }  
        }  
    }   

    // TODO let this be incremented reactive by each kid thats correct -> Saves computing power
    let numberOfCorrectBottles;

    $: console.log("I selected Bottle ", selectedBottle);
</script>  
  
<div class="game-board">  
    {#each gameState.bottles as bottle, bottleIndex (bottleIndex)}  
        <div class={'bottle'}>  
            <Bottle {bottle} id={bottleIndex} 
                    bind:bottleFinished={finishedBottles[bottleIndex]}
                    on:click={() => selectOrPour(bottleIndex)} 
                    selected={bottleIndex === selectedBottle} 
                    pouring={bottleIndex === pouringBottle} />  
        </div>
    {/each}  
</div>  

<style>
    .bottle {
        display: inline-block;
        margin: 10px;
    }
</style>