<script lang="ts">
	import { onMount } from 'svelte';
	import { browser } from '$app/environment';
	import { FFmpeg } from '@ffmpeg/ffmpeg';
	import { fetchFile, toBlobURL } from '@ffmpeg/util';

	let { canvas }: { canvas: HTMLCanvasElement | undefined } = $props();

	let mediaRecorder: MediaRecorder | null = null;
	let recordedChunks: Blob[] = [];
	let isRecording = $state(false);
	let recordingTime = $state(0);
	let recordingInterval: number | null = null;
	let targetDuration = 10; // 10 seconds
	let isConverting = $state(false);
	let ffmpeg: FFmpeg | null = null;
	let ffmpegLoaded = $state(false);
	let autoRecord = $state(false);

	// Load autoRecord preference from localStorage
	if (browser) {
		const saved = localStorage.getItem('autoRecord');
		if (saved !== null) {
			autoRecord = saved === 'true';
		}
	}

	// Save autoRecord preference to localStorage when it changes
	$effect(() => {
		if (browser) {
			localStorage.setItem('autoRecord', String(autoRecord));
		}
	});

	function startRecording() {
		if (!browser || !canvas) return;

		const stream = canvas.captureStream(60); // 60 FPS
		recordedChunks = [];

		// Try to use MP4 if supported, fallback to WebM
		const mimeType = MediaRecorder.isTypeSupported('video/mp4')
			? 'video/mp4'
			: MediaRecorder.isTypeSupported('video/webm;codecs=vp9')
			? 'video/webm;codecs=vp9'
			: 'video/webm';

		mediaRecorder = new MediaRecorder(stream, {
			mimeType,
			videoBitsPerSecond: 8000000 // 8 Mbps for high quality
		});

		mediaRecorder.ondataavailable = (event) => {
			if (event.data.size > 0) {
				recordedChunks.push(event.data);
			}
		};

		mediaRecorder.onstop = async () => {
			const blob = new Blob(recordedChunks, { type: mimeType });

			// Convert WebM to MP4 for iPhone compatibility
			if (mimeType.includes('webm') && ffmpegLoaded) {
				await convertToMP4(blob);
			} else {
				// Fallback: download as-is
				downloadFile(blob, mimeType);
			}
		};

		mediaRecorder.start();
		isRecording = true;
		recordingTime = 0;

		// Update timer and auto-stop
		recordingInterval = window.setInterval(() => {
			recordingTime++;
			if (recordingTime >= targetDuration) {
				stopRecording();
			}
		}, 1000);
	}

	function stopRecording() {
		if (mediaRecorder && mediaRecorder.state !== 'inactive') {
			mediaRecorder.stop();
			mediaRecorder = null;
		}
		if (recordingInterval) {
			clearInterval(recordingInterval);
			recordingInterval = null;
		}
		isRecording = false;
	}

	async function convertToMP4(webmBlob: Blob) {
		if (!ffmpeg) return;

		isConverting = true;

		try {
			// Write input file
			await ffmpeg.writeFile('input.webm', await fetchFile(webmBlob));

			// Convert to MP4 with iPhone-compatible settings
			await ffmpeg.exec([
				'-i', 'input.webm',
				'-c:v', 'libx264',           // H.264 codec
				'-profile:v', 'baseline',     // Baseline profile for maximum compatibility
				'-level', '3.0',              // Level 3.0 for older devices
				'-pix_fmt', 'yuv420p',        // Required pixel format for Apple devices
				'-movflags', '+faststart',    // Enable streaming/web playback
				'-preset', 'medium',          // Balance between speed and quality
				'-crf', '23',                 // Quality (lower = better, 23 is good default)
				'output.mp4'
			]);

			// Read output file
			const data = await ffmpeg.readFile('output.mp4');
			const mp4Blob = new Blob([data], { type: 'video/mp4' });

			downloadFile(mp4Blob, 'video/mp4');
		} catch (error) {
			console.error('Conversion failed:', error);
			// Fallback to WebM
			downloadFile(webmBlob, 'video/webm');
		} finally {
			isConverting = false;
		}
	}

	function downloadFile(blob: Blob, mimeType: string) {
		const url = URL.createObjectURL(blob);
		const a = document.createElement('a');
		a.href = url;
		a.download = `orbit-glitter-${Date.now()}.${mimeType.includes('mp4') ? 'mp4' : 'webm'}`;
		a.click();
		URL.revokeObjectURL(url);
	}

	onMount(async () => {
		// Load FFmpeg
		ffmpeg = new FFmpeg();
		try {
			const baseURL = 'https://unpkg.com/@ffmpeg/core@0.12.6/dist/esm';
			await ffmpeg.load({
				coreURL: await toBlobURL(`${baseURL}/ffmpeg-core.js`, 'text/javascript'),
				wasmURL: await toBlobURL(`${baseURL}/ffmpeg-core.wasm`, 'application/wasm')
			});
			ffmpegLoaded = true;
		} catch (error) {
			console.error('FFmpeg failed to load:', error);
		}

		// Auto-start recording if enabled
		if (autoRecord && canvas) {
			setTimeout(() => {
				startRecording();
			}, 100);
		}
	});
</script>

<div class="controls">
	{#if isConverting}
		<div class="progress">Converting to MP4...</div>
	{:else if isRecording}
		<div class="progress">
			Recording: {recordingTime}s / {targetDuration}s ({Math.round((recordingTime / targetDuration) * 100)}%)
		</div>
		<button onclick={stopRecording} class="recording">Stop Recording</button>
	{:else}
		<div class="control-row">
			<button onclick={startRecording}>Start Recording</button>
			<label class="auto-record-toggle">
				<input type="checkbox" bind:checked={autoRecord} />
				Auto-record on load
			</label>
		</div>
	{/if}
</div>

<style>
	.controls {
		display: flex;
		flex-direction: column;
		gap: 0.5rem;
		align-items: center;
	}

	.control-row {
		display: flex;
		gap: 1rem;
		align-items: center;
	}

	.progress {
		font-size: 0.9rem;
		color: hsl(249, 28%, 70%);
		padding: 0.5rem;
		background-color: hsl(240, 30%, 15%);
		border-radius: 4px;
		font-family: monospace;
	}

	.auto-record-toggle {
		display: flex;
		align-items: center;
		gap: 0.5rem;
		color: hsl(249, 28%, 85%);
		cursor: pointer;
		font-size: 0.9rem;
		user-select: none;
	}

	.auto-record-toggle input[type="checkbox"] {
		cursor: pointer;
		width: 1.2rem;
		height: 1.2rem;
	}

	button {
		padding: 0.75rem 1.5rem;
		font-size: 1rem;
		font-weight: 500;
		color: hsl(249, 28%, 85%);
		background-color: hsl(240, 30%, 20%);
		border: 2px solid hsl(240, 30%, 30%);
		border-radius: 6px;
		cursor: pointer;
		transition: all 0.2s;
	}

	button:hover:not(:disabled) {
		background-color: hsl(240, 30%, 25%);
		border-color: hsl(240, 30%, 40%);
	}

	button:disabled {
		opacity: 0.5;
		cursor: not-allowed;
	}

	button.recording {
		background-color: hsl(0, 70%, 40%);
		border-color: hsl(0, 70%, 50%);
		animation: pulse 2s ease-in-out infinite;
	}

	button.recording:hover {
		background-color: hsl(0, 70%, 45%);
		border-color: hsl(0, 70%, 60%);
	}

	@keyframes pulse {
		0%, 100% {
			opacity: 1;
		}
		50% {
			opacity: 0.7;
		}
	}
</style>
