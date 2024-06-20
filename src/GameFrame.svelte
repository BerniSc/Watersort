<!-- This displayes the Game, as well as Addition Game Elements -->
<script>
    import Modal, { getModal } from "./lib/Modal.svelte";
</script>


<div class="card">
    <slot></slot>  
</div>
<button on:click>Restart</button>
<button on:click={() => getModal().show()}>Show the Modal</button>
<input type="range"  />

<Modal> 
    <h1>Play the Watersort Game here</h1>
    <p>🎉Für nur 7475€🎉</p>
    <button on:click={() => getModal("two").show()}>Long live the nested Modals</button>
</Modal>

<Modal id="two">
    <h2>Second One</h2>
    <p>Yeah</p>
</Modal>
