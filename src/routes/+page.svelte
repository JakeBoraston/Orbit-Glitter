<script lang="ts">
	import { onMount } from 'svelte';

	let canvas: HTMLCanvasElement;
	let ctx: CanvasRenderingContext2D | null;

	let windowWidth = $state<number>();
	let windowHeight = $state<number>();

	let width = $derived(windowWidth*0.8)
	let height = $derived(windowHeight*0.8)

	onMount(() => {
		windowWidth = window.innerWidth;
		windowHeight = window.innerHeight;
		const context = canvas.getContext('2d');
		ctx = context;
		ctx.fillStyle = 'white';
		ctx.fillRect(1, 1, 100, 100);
	});
</script>
<svelte:window bind:innerWidth={windowWidth} bind:innerHeight={windowHeight} />

<canvas bind:this={canvas} id="canvas1" width={width} height={height}></canvas>

<style>
	canvas {
		background-color: antiquewhite;
		aspect-ratio: 1;
		background-color: #010101;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		border-radius: 4px;
		box-shadow: hsl(240, 30%, 10%) 0 0 50px;
	}
</style>
