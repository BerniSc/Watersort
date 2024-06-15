<script>  
    import GameBoard from './GameBoard.svelte';  
    import GameFrame from './GameFrame.svelte';  

    // Define the possible colors for the water levels.  
    const possibleColors = ['blue', 'red', 'orange', 'green', 'purple', 'yellow'];  
  
    const BOTTLE_CAPACITY = 5;

    // shuffle array (Fisher-Yates shuffle algorithm)
    function shuffle(array) {  
        for(let i = array.length - 1; i > 0; i--) {  
            const j = Math.floor(Math.random() * (i + 1));  
            [array[i], array[j]] = [array[j], array[i]];  
        }  
        return array;
    }  
  
    function generateRandomGameState(numberOfBottles) {  
        // Total Levels needed
        let totalLevels = (numberOfBottles - 2) * BOTTLE_CAPACITY;  
        let colorStack = [];  
  
        // Calculate the maximum number of full sets of different colors we can have  
        const fullColorSets = Math.max(Math.floor(totalLevels / (possibleColors.length * BOTTLE_CAPACITY)), 1);  
        console.log(fullColorSets);
  
        // Start by adding full sets of each color  
        for(let i = 0; i < fullColorSets; i++) 
            for(let color of [...shuffle(possibleColors)]) {
                for(let j = 0; j < BOTTLE_CAPACITY; j++) {
                    colorStack.push({ color: color });  
                    totalLevels -= 1;
                }
                console.log("Filled x Leves here");
                if(totalLevels === 0) break;
            }
  
    
        // Fill up the remaining levels with a random selection of colors  
        while(totalLevels > 0) {  
            let index = Math.floor(Math.random() * possibleColors.length);
            for(let j = 0; j < BOTTLE_CAPACITY; j++)
                colorStack.push({ color: possibleColors[index] });  
            totalLevels -= BOTTLE_CAPACITY;  
        }
    
        // Shuffle the stack to randomize the color distribution  
        shuffle(colorStack);  
    
        // Create the bottles and distribute colors from the stack into the bottles  
        let bottles = Array.from({ length: numberOfBottles }, () => ({ levels: [] }));  
        let bottleIndex = 0;  
    
        while(colorStack.length > 0) {  
            // Fill the next bottle with colors from the stack  
            bottles[bottleIndex].levels = colorStack.splice(0, BOTTLE_CAPACITY);  
            bottleIndex++;  
            // Skip over the last two bottles to leave them empty  
            if(bottleIndex === numberOfBottles - 2) 
                bottleIndex += 2;  
        }  
    
        // Shuffle the bottles to mix filled and empty ones  
        shuffle(bottles);  
    
        return { bottles }; // Return an object with a 'bottles' property  
    }  

    // The intial Game State 
    let gameStates = {  
        bottles: [  
            { id: 1, levels: [{ color: 'yellow' }, { color: 'yellow' }, { color: 'yellow'}, { color: 'yellow'}, { color: 'yellow'}]},  
            { id: 2, levels: [{ color: 'yellow' }] },  
            { id: 2, levels: [{ color: 'green' }] },  
            { id: 2, levels: [{ color: 'red' }] },  
        ]  
    };  

    let gameState = generateRandomGameState(4);
</script>  
  
<GameFrame>  
    <GameBoard bind:gameState={gameState} />  
</GameFrame>  
