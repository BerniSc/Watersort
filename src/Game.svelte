<script>  
  import GameBoard from './GameBoard.svelte';  
  import GameFrame from './GameFrame.svelte';  
    
  // Anfangszustand des Spiels als JSON-Objekt  
  let gameState = {  
    bottles: [  
      { id: 1, levels: [{ color: 'blue' }, { color: 'red' }, { color: 'yellow'}, { color: 'green'}, { color: 'purple'}]},  
      { id: 2, levels: [{ color: 'yellow' }] },  
    ]  
  };  
</script>  
  
<GameFrame>  
  <GameBoard bind:gameState={gameState} />  
</GameFrame>  
