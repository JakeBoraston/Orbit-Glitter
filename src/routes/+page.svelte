<script lang="ts">
	import { onMount } from 'svelte';
	
	let canvas:HTMLCanvasElement;
	let ctx:CanvasRenderingContext2D|null;
	
	let height = $state<number>();
	let width = $state<number>();

	onMount(() => {
		const context = canvas.getContext("2d")
		ctx = context;
	});
</script>

<main bind:clientHeight={height} bind:clientWidth={width}>
	<canvas bind:this={canvas} id="canvas1"></canvas>
</main>

<style>
	main {
		height: 85%;
		width: 85%;
		background-color: #010101;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		border-radius: 4px;
		box-shadow: hsl(240, 30%, 10%) 0 0 50px;
	}
</style>
