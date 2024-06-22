<script>  
    import GameBoard from './GameBoard.svelte';  
    import GameFrame from './GameFrame.svelte';  

    import Confetti from './lib/Confetti.svelte';
    let confettiObject;

    import { strGlassNumber } from './stores/state';

    // Define the possible colors for the water levels.  
    const possibleColors = ['blue', 'red', 'orange', 'green', 'purple', 'yellow', 'pink', 'brown', ];  
  
    const BOTTLE_CAPACITY = 5;

    // shuffle array (Fisher-Yates shuffle algorithm)
    function shuffle(array) {  
        for(let i = array.length - 1; i > 0; i--) {  
            const j = Math.floor(Math.random() * (i + 1));  
            [array[i], array[j]] = [array[j], array[i]];  
        }  
        return array;
    }  
  
    function generateRandomGameState(numberOfBottles, numberFreeBottles = 2) {  
        // Total Levels needed
        let totalLevels = (numberOfBottles - numberFreeBottles) * BOTTLE_CAPACITY;  
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
            if(bottleIndex === numberOfBottles - numberFreeBottles) 
                bottleIndex += numberFreeBottles;  
        }  
    
        // Shuffle the bottles to mix filled and empty ones  
        shuffle(bottles);  
    
        return { bottles }; // Return an object with a 'bottles' property  
    }  

    // Serialize the current Game-State for Loop Detection
    function serializeGameState(gameState) {  
        // Convert the game state into a string representation  
        return gameState.bottles.map(bottle =>   
            bottle.levels.map(level => level.color).join(',')  
        ).join('|');  
    }  
  
    function canMakeMoves(gameState, depth = 0, maxDepth = 3, visitedStates = new Set()) {  
        // Serialize the current game state  
        const stateKey = serializeGameState(gameState);  
    
        // If we've already visited this state or reached the max depth, return false  
        if (visitedStates.has(stateKey) || depth >= maxDepth) {  
            return false;  
        }  
    
        // Add the current state to the visited states  
        visitedStates.add(stateKey);  
    
        // Iterate over all pairs of bottles to check for possible moves  
        for(let i = 0; i < gameState.bottles.length; i++) {  
            for(let j = 0; j < gameState.bottles.length; j++) {  
                if(i !== j && isValidMove(gameState.bottles[i], gameState.bottles[j])) {  
                    // Clone the game state to avoid mutating the original state  
                    let clonedGameState = deepCloneArray(gameState);  
    
                    // Perform the move  
                    mockPerformMove(clonedGameState.bottles[i], clonedGameState.bottles[j]);  
    
                    // Serialize the new game state after the move  
                    const newStateKey = serializeGameState(clonedGameState);  
    
                    // If the new state has not been visited, or if we are not at the max depth,  
                    // we can say a valid move is possible (without being in a loop).  
                    if(!visitedStates.has(newStateKey)) {  
                        // If we're not at maxDepth yet, we can check the next level  
                        if(depth + 1 < maxDepth) {  
                            // Make a recursive call to check the next level  
                            if(canMakeMoves(clonedGameState, depth + 1, maxDepth, visitedStates)) {  
                                return true;  
                            }  
                        } else {  
                            // If we are at maxDepth, we've found a valid move that doesn't immediately loop  
                            return true;  
                        }  
                    }  
                }  
            }  
        }  
    
        // If we haven't returned true by now, no further moves are possible without a loop  
        return false;  
    }  
  
    // Check if a move is valid from one bottle to another
    function isValidMove(fromBottle, toBottle) {  
        if(fromBottle.levels.length === 0) return false;
        const fromTopLevelColor = fromBottle.levels[fromBottle.levels.length - 1].color;  
        const toTopLevelColor = toBottle.levels.length > 0 ? toBottle.levels[toBottle.levels.length - 1].color : null;  
        const availableSpace = BOTTLE_CAPACITY - toBottle.levels.length;
        return (fromTopLevelColor === toTopLevelColor || toTopLevelColor === null) && availableSpace > 0;  
    }  
  
    // Perform a move by pouring water from one bottle to another  
    // TODO find better way to have this Code not be duplicated here and in GameBoard.svelte. That kinda sucks, but works for now. Still... Not happy
    function mockPerformMove(fromBottle, toBottle) {  
        const fromTopLevelColor = fromBottle.levels[fromBottle.levels.length - 1].color;  
        const availableSpace = BOTTLE_CAPACITY - toBottle.levels.length;  
        let levelsToPour = 0;  
        for(let i = fromBottle.levels.length - 1; i >= 0; i--) {  
            if(fromBottle.levels[i].color === fromTopLevelColor)  
                levelsToPour++;  
            else  
                break;  
        }  
        levelsToPour = Math.min(levelsToPour, availableSpace);  
    
        // Perform the pour  
        const pouredLevels = fromBottle.levels.splice(fromBottle.levels.length - levelsToPour, levelsToPour);  
        toBottle.levels.push(...pouredLevels);          // Add the poured levels to the 'toBottle'  
    }  
  

    let gameState = generateRandomGameState($strGlassNumber, 2);

    // We want a function to DeepClone an array, so that changes to it dont affect the original like in a normal Lang like C/C++ :-)
    function deepCloneArray(gameState) {  
        return {  
            bottles: gameState.bottles.map(bottle => ({  
                ...bottle,  
                levels: [...bottle.levels]  
            }))  
        };  
    }  

    $: {
        let test;
        console.log("Is winnable ", test=canMakeMoves(gameState));
        if(!test) alert("Not Winnable");
    }

    $: {
        if($strGlassNumber)
            gameState = generateRandomGameState($strGlassNumber, 2);
    }

    function wonCurrentBoard() {
        confettiObject.startConfetti();
    }
</script>  
  
<GameFrame on:click={() => gameState = generateRandomGameState($strGlassNumber, 2)}>  
    <GameBoard bind:gameState={gameState} on:winCurrentBoard={wonCurrentBoard} />  
    <Confetti bind:this={confettiObject} />
</GameFrame>  
