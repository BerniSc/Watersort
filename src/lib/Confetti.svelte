<script>
    import { onMount } from 'svelte';
    import confetti from 'canvas-confetti';

    let startConfetti = null;
    
    export let durationInS = 3;
    export let startVelocity = 30;
    export let origin = { x: 0.5, y: 0.5 };

    onMount(() => {
        startConfetti = () => {
            const duration = durationInS * 1000;
            const animationEnd = Date.now() + duration;
            const defaults = { startVelocity: startVelocity, spread: 360, ticks: 60, zIndex: 0 };

            function randomInRange(min, max) {
                return Math.random() * (max - min) + min;
            }

            (function frame() {
                confetti(Object.assign({}, defaults, {
                    particleCount: 5,
                    origin: { origin }
                }));

                if (Date.now() < animationEnd) {
                    requestAnimationFrame(frame);
                }
            }());
        };
    });

    export { startConfetti };
</script>

<svelte:options accessors={true} />
<div id="confetti-canvas"></div>