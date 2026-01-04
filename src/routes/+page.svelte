<script lang="ts">
	import { onMount } from 'svelte';

	let windowWidth = $state<number>();
	let windowHeight = $state<number>();

	let width = $derived(windowWidth * 0.8);
	let height = $derived(windowHeight * 0.8);

	let canvas: HTMLCanvasElement;
	let ctx: CanvasRenderingContext2D | null;

	onMount(() => {
		//get window dimensions
		windowWidth = window.innerWidth;
		windowHeight = window.innerHeight;

		//set context api
		const context = canvas.getContext('2d');
		ctx = context;

		let lastTime = performance.now();

		// svelte-ignore perf_avoid_nested_class
		class Walker {
			x: number;
			y: number;
			xv: number;
			xy: number;
			constructor() {
				this.x = width / 2;
				this.y = height / 2;
				this.xv = 100;
				this.xy = 80;
			}
			show(delta:number) {
				ctx.fillStyle = 'white';
				ctx.fillRect(this.x, this.y, 2, 2);
				walker.x = walker.x + walker.xv * delta;
			}
		}

		let walker = new Walker();

		function draw(time: number) {
			const delta = (time - lastTime) / 1000;
			lastTime = time;

			walker.show(delta);

			requestAnimationFrame(draw);
		}

		requestAnimationFrame(draw);
	});

	$effect(() => {
		if (!canvas) return;

		canvas.width = width;
		canvas.height = height;
	});
</script>

<svelte:window bind:innerWidth={windowWidth} bind:innerHeight={windowHeight} />

<canvas bind:this={canvas} id="canvas1" {width} {height}></canvas>

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
