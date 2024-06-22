<script>
    import Slider from "./lib/Slider.svelte";

    import { strGlassNumber } from "./stores/state";
</script>

<div class="settings-container">
    <h1>Settings</h1>
    <div class="list">
        <div class="list-item">
            <span class="list-item-lbl">Number of Bottles:</span>
            <Slider min={3} max={12} hideMinMax={false} bind:value={$strGlassNumber} class="list-item-value" />
        </div>
    </div>
</div>

<style>
    .settings-container {
        display: flex;
        width: 90%;
        justify-content: center;
        flex-direction: column;
        /* border: 2px solid red; */
    }

    .list {
        width: 100%;
        /* border: 1px solid green; */
    }

    .list-item {
        width: 100%;
        display: flex;
        gap: 5px;
    }

    .list-item-lbl {

    }

    .settings-container :global(.list-item-value) {
        width: 100%;
    }

</style>