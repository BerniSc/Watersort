<!-- Modal.svelte -->
<!-- Courtesy of https://svelte.dev/repl/514f1335749a4eae9d34ad74dc277f20?version=3.37.0 -->
<!-- Changed from There -->

<!-- "Static" Vars -->
<script context="module" lang="ts">
	let onTop;              //keeping track of which open modal is on top
	const modals={};        //all modals get registered here for easy future access
	
	// 	returns an object for the modal specified by `id`, which contains the API functions (`open` and `close` )
	export function getModal(id=''){
		return modals[id]
	}
</script>

<script lang="ts">
    import {onDestroy} from 'svelte';
        
    let topDiv;
    let visible=false;
    let prevOnTop;
    let closeCallback;

    export let closeOnEscape = true;
    export let id='';

    function keyPress(ev){
        //only respond if the current modal is the top one
        // If yes, then Close on escape
        if(ev.key == "Escape" && closeOnEscape && onTop == topDiv)
            close();
    }

    /**  API **/
    function show(callback) {
        closeCallback = callback;

        if(visible) 
            return;

        prevOnTop = onTop;
        onTop = topDiv;

        window.addEventListener("keydown", keyPress);
        
        //this prevents scrolling of the main window on larger screens
        document.body.style.overflow = "hidden";

        visible = true;

        //Move the modal in the DOM to be the last child of <BODY> so that it can be on top of everything
        document.body.appendChild(topDiv)
    }
	
    function close(retVal){
        if(!visible)
            return;

        window.removeEventListener("keydown", keyPress);
        onTop = prevOnTop;
        if(onTop == null) 
            document.body.style.overflow = "";
        visible = false;
        if(closeCallback)
            closeCallback(retVal);
    }
	
    //expose the API
    modals[id]={show, close}
	
    onDestroy(()=>{
        delete modals[id];
        window.removeEventListener("keydown", keyPress);
    });
</script>

<div id="topModal" class:visible class="window-wrap" bind:this={topDiv} on:click={()=>close()} role="dialog" aria-modal="true">
	<div id='modal' on:click|stopPropagation={()=>{}}>
        <button id="close" class="close" on:click={() => close()} />
		<div id='modal-content'>
			<slot></slot>
		</div>
	</div>
</div>

<style>
	#topModal {
		visibility: hidden;
		z-index: 9999;
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background: #4448;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	#modal {
        position: relative;
        width: 40rem;
        max-width: 100%;
        max-height: 100%;
        margin: 2rem auto;
        color: black;
        border-radius: 0.5rem;
        background: white;
	}

	#modal-content {
        position: relative;
        padding: 1rem;
        max-height: calc(100vh - 4rem);
		max-width: calc(100vw - 4rem);
        overflow: auto;
	}

	.visible {
		visibility: visible !important;
	}

    .close {
        display: block;
        box-sizing: border-box;
        position: absolute;
        z-index: 1000;
        top: 1rem;
        right: 1rem;
        margin: 0;
        padding: 0;
        width: 1.5rem;
        height: 1.5rem;
        border: 0;
        color: black;
        border-radius: 1.5rem;
        background: white;
        box-shadow: 0 0 0 1px black;
        transition: transform 0.2s cubic-bezier(0.25, 0.1, 0.25, 1),
        background 0.2s cubic-bezier(0.25, 0.1, 0.25, 1);
        -webkit-appearance: none;
    }

    .close:before,
    .close:after {
        content: '';
        display: block;
        box-sizing: border-box;
        position: absolute;
        top: 50%;
        width: 1rem;
        height: 1px;
        background: black;
        transform-origin: center;
        transition: height 0.2s cubic-bezier(0.25, 0.1, 0.25, 1),
        background 0.2s cubic-bezier(0.25, 0.1, 0.25, 1);
    }

    .close:before {
        -webkit-transform: translate(0, -50%) rotate(45deg);
        -moz-transform: translate(0, -50%) rotate(45deg);
        transform: translate(0, -50%) rotate(45deg);
        left: 0.25rem;
    }

    .close:after {
        -webkit-transform: translate(0, -50%) rotate(-45deg);
        -moz-transform: translate(0, -50%) rotate(-45deg);
        transform: translate(0, -50%) rotate(-45deg);
        left: 0.25rem;
    }

    .close:hover {
        background: black;
    }

    .close:hover:before,
    .close:hover:after {
        height: 2px;
        background: white;
    }

    .close:focus {
        border-color: #3399ff;
        box-shadow: 0 0 0 2px #3399ff;
    }

    .close:active {
        transform: scale(0.9);
    }

    .close:hover,
    .close:focus,
    .close:active {
        outline: none;
    }
</style>
