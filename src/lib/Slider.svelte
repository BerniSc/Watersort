<script lang="ts">
    let holding;
    let thumbHover;

    export let min = 0;
    export let max = 100;
    export let value = (max - min) / 2;
    export let hideMinMax = true;

    import { fly, fade } from "svelte/transition";

    let classname = '';
    export { classname as class };
</script>

<div class="range__wrapper {classname}">
    <div style="display: flex; width: 100%; align-items: center; gap: 5px;">
        <span class="range__label" class:invisible={hideMinMax}>{min}</span>
        <input type="range" 
            class="range__style"
            class:range__thumb--holding={holding}
            on:mouseover={() => (thumbHover = true)}
            on:mouseout={() => (thumbHover = false)}
            on:focus
            on:blur
            bind:value
            min={min}
            max={max}
            />
        <span class="range__label" class:invisible={hideMinMax}>{max}</span>
    </div>

    {#if holding || thumbHover}
        <div
            class="range__tooltip"
            in:fly={{ y: 7, duration: 200 }}
            out:fade={{ duration: 100 }}
            >
            {value}
        </div>
    {/if}
</div>

<style>
    .invisible {
        visibility: hidden;
    }

    .range__wrapper {
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .range__label {
        user-select: none;
        margin: auto;
    }

    .range__style {
        width: 100%;
    }

    .range__tooltip {
        display: flex;
        background-color: antiquewhite;
        background: var(--tooltip-bg, linear-gradient(45deg, antiquewhite, burlywood));
        width: 38px;
        border-radius: 4px;
        align-items: center;
        justify-content: center;
    }
</style>