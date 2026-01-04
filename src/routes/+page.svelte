<script lang="ts">
	import { onMount } from 'svelte';

	let windowWidth = $state<number>();
	let windowHeight = $state<number>();

	let width = $derived(windowWidth * 0.8);
	let height = $derived(windowHeight * 0.8);

	const numberOfWalkers = 100;

	let canvas: HTMLCanvasElement;
	let ctx: CanvasRenderingContext2D | null;

	class Walker {
		x: number;
		y: number;
		r: number;
		velocity: number;
		size: number;
		hue: number;
		color: string;
		angle: number;
		radius: number;
		cx: number;
		cy: number;
		constructor(size: number,hue:number) {
			this.size = size;
			this.r = Math.random();
			this.x = Math.random() * width - this.size;
			this.y = Math.random() * height - this.size;
			this.velocity = Math.random() * 5
			this.hue = hue
			this.color = `hsl${this.hue},90%,50%`;
			this.cx = width / 2;
			this.cy = height / 2;
			this.angle = Math.random() * Math.PI * 2;
			this.radius = 5 + Math.random() * (width * 0.4);
		}
		show(delta: number) {
			this.spin(delta);
			ctx.fillStyle = `hsl(${this.hue},90%,50%)`;
			ctx.fillRect(this.x, this.y, this.size, this.size);
			// this.setRandDirection(delta);
			//bounding to canvas
			this.x = Math.max(this.size, Math.min(width - this.size, this.x));
			this.y = Math.max(this.size, Math.min(height - this.size, this.y));
		}

		spin(delta: number) {
			this.angle += this.velocity * delta + 1;

			this.x = this.cx + Math.cos(this.angle) * this.radius;
			this.y = this.cy + Math.sin(this.angle) * this.radius;
		}

		setRandDirection(delta:number) {
			const randNum = Math.floor(Math.random() * 4);
			const speed = this.velocity * delta; 
			switch (randNum) {
				case 0:
					this.x = this.x + 1 * speed;
					break;
				case 1:
					this.x = this.x - 1 * speed;
					break;
				case 2:
					this.y = this.y + 1 * speed;
					break;
				case 3:
					this.y = this.y - 1 * speed;
					break;
			}
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

		let walkers = [];
		const hue = Math.random() * 360
		for (let index = 0; index < numberOfWalkers; index++) {
			walkers.push(new Walker(Math.random()*3,hue+index));
		}

		function draw(time: number) {
			const delta = (time - lastTime) / 1000;
			lastTime = time;
			// ctx?.clearRect(0, 0, width, height);
			ctx.fillStyle = 'rgba(0,0,0,0.01)';
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

<canvas bind:this={canvas} id="canvas1" {width} {height}></canvas>

<style>
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
</style>
