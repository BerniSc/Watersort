<script>  
    import GameBoard from './GameBoard.svelte';  
    import GameFrame from './GameFrame.svelte';  
    
    // The intial Game State 
    let gameState = {  
        bottles: [  
            { id: 1, levels: [{ color: 'yellow' }, { color: 'yellow' }, { color: 'yellow'}, { color: 'yellow'}, { color: 'yellow'}]},  
            { id: 2, levels: [{ color: 'yellow' }] },  
        ]  
    };  
</script>  
  
<GameFrame>  
    <GameBoard bind:gameState={gameState} />  
</GameFrame>  
