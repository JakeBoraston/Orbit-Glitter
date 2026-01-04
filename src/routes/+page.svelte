<script lang="ts">
	import { onMount } from 'svelte';

	let windowWidth = $state<number>();
	let windowHeight = $state<number>();

	let width = $derived(windowWidth * 0.8);
	let height = $derived(windowHeight * 0.8);

	const numberOfWalkers = 10;

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
			constructor(size:number) {
				this.size = size
				this.r = Math.random();
				this.x = Math.random() * width - this.size;
				this.y = Math.random() * height - this.size;
				this.velocity = 50;
				this.hue = this.r * 360;
				this.color = `hsl${this.hue},90%,50%`;
			}
			show(delta: number) {
				const randNum = Math.floor(Math.random() * 4);
				const speed = (this.size * this.velocity) * delta;
				ctx.fillStyle = `hsl(${this.hue},90%,50%)`;
				ctx.fillRect(this.x, this.y, this.size, this.size);
				this.setDirection(randNum, speed);
				this.x = Math.max(this.size, Math.min(width - this.size, this.x));
				this.y = Math.max(this.size, Math.min(height - this.size, this.y));
			}

			setDirection(randNum: number, speed: number) {
				switch (randNum) {
					case 0:
						this.x = this.x + speed;
						break;
					case 1:
						this.x = this.x - speed;
						break;
					case 2:
						this.y = this.y + speed;
						break;
					case 3:
						this.y = this.y - speed;
						break;
				}
			}

			checkBounds() {
				//check left border
				if (this.x + this.size <= 0) {
					this.x = 0 + this.size;
				}
				//check right border
				if (this.x - this.size >= width) {
					this.x = width - this.size;
				}
				//check top
				if (this.y + this.size <= 0) {
					this.y = 0 + this.size;
				}
				//check bottom
				if (this.y >= height - this.size) {
					this.y = height - this.size;
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
		for (let index = 0; index < numberOfWalkers; index++) {
			walkers.push(new Walker(Math.min(Math.random()*4,2)));
		}

		function draw(time: number) {
			const delta = (time - lastTime) / 1000;
			lastTime = time;
			// ctx?.clearRect(0, 0, width, height);

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
