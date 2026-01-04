<script lang="ts">
	import { onMount } from 'svelte';
	import RecordingControls from '$lib/RecordingControls.svelte';

	let windowWidth = $state<number>();
	let windowHeight = $state<number>();

	let width = $derived(windowWidth * 0.8);
	let height = $derived(windowHeight * 0.8);

	let bgAlpha = $state(0.009);
	let hueSeperation = $state(1);
	let hue = $state(Math.random() * 360);
	let radiusMul = $state(0.45);

	let walkers = $state([]);

	const numberOfWalkers = 100;

	let canvas: HTMLCanvasElement;
	let ctx: CanvasRenderingContext2D | null;

	class Walker {
		x: number;
		y: number;
		velocity: number;
		size: number;
		hue: number;
		angle: number;
		radius: number;
		cx: number;
		cy: number;
		constructor(size: number, hue: number) {
			this.size = size;
			this.x = 0;
			this.y = 0;
			this.velocity = Math.random() * 1;
			this.hue = hue;
			this.cx = width / 2;
			this.cy = height / 2;
			this.angle = Math.random() * Math.PI * 2;
			this.radius = 5 + Math.random() * (width * radiusMul);
		}
		show(delta: number) {
			this.spin(delta);
			ctx.fillStyle = `hsl(${this.hue},90%,50%)`;
			ctx.fillRect(this.x, this.y, this.size, this.size);
		}

		spin(delta: number) {
			this.angle += this.velocity * delta + 1;
			this.x = this.cx + Math.cos(this.angle) * this.radius;
			this.y = this.cy + Math.sin(this.angle) * this.radius;
		}
	}

	function populate(restart: boolean) {
		restart ? (walkers = []) : '';
		for (let index = 0; index < numberOfWalkers; index++) {
			walkers.push(new Walker(Math.random() * 2, hue + index * hueSeperation));
		}
	}

	onMount(() => {
		//get window dimensions
		windowWidth = window.innerWidth;
		windowHeight = window.innerHeight;

		//set context api
		const context = canvas.getContext('2d');
		ctx = context;

		let lastTime = performance.now();

		// svelte-ignore perf_avoid_nested_class

		populate(false);

		function draw(time: number) {
			const delta = (time - lastTime) / 1000;
			lastTime = time;
			// ctx?.clearRect(0, 0, width, height);
			ctx.fillStyle = `rgba(0,0,0,${bgAlpha})`;
			ctx.fillRect(0, 0, width, height);

			for (const walker of walkers) {
				walker.show(delta);
			}

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

<div class="container">
	<canvas bind:this={canvas} id="canvas1" {width} {height}></canvas>

	<RecordingControls {canvas} />
	<section class="controls">
		<label for="alpha"
			>Alpha
			<input bind:value={bgAlpha} min="0" max="0.1" step="0.01" type="range" id="alpha" />
		</label>
		<label for="hueSeperation"
			>Hue Seperation
			<input
				bind:value={hueSeperation}
				min="0"
				max="2"
				step="0.2"
				type="range"
				id="hueSeperation"
				onchange={() => {
					populate(true);
				}}
			/>
		</label>
		<label for="hue"
			>Hue
			<input
				bind:value={hue}
				min="0"
				max="360"
				step="2"
				type="range"
				id="hue"
				onclick={() => {
					populate(true);
				}}
			/>
		</label>
		<label for="radiusMul"
			>Radius
			<input
				bind:value={radiusMul}
				min="0"
				max="1"
				step="0.1"
				type="range"
				id="radiusMul"
				onclick={() => {
					populate(true);
				}}
			/>
		</label>
	</section>
</div>

<style>
	.container {
		display: flex;
		flex-direction: column;
		gap: 1rem;
		align-items: center;
	}

	canvas {
		background-color: antiquewhite;
		background-color: #010101;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		border-radius: 4px;
		box-shadow: hsl(240, 30%, 10%) 0 0 50px;
		max-width: 90svw;
		max-height: 90svh;
	}
	.controls{
		display: flex;
		flex-wrap: wrap;
		max-width: 80svw;
		overflow-x: hidden;
		justify-content: safe;
		align-items: center;
		gap:0px 8px;
	}

	.controls input {
		width: 100px;
		
	}
</style>
