<script>
	import { createEventDispatcher, onDestroy, onMount } from 'svelte';
	import loadImage from 'image-promise';

	export let manifest;
	export let playing = false;
	export let loading = true;

	export let autoPlay = false;
	export let loop = false;

	const dispatch = createEventDispatcher();

	let canvas;
	let images = [];
	$: context = canvas?.getContext('2d');

	let interval;
	let currentFrameIndex = 0;
	$: currentFrame = manifest.frames[currentFrameIndex];

	export function start() {
		if (loading || playing) return;
		reset();
		playing = true;
		interval = setInterval(() => {
			if (currentFrameIndex < manifest.frames.length - 1) {
				currentFrameIndex += 1;
				requestAnimationFrame(() => {
					context.clearRect(0, 0, canvas.width, canvas.height);
					context.drawImage(images[currentFrameIndex], 0, 0);
				});
			} else {
				if (!loop) stop();
				if (loop) reset();
			}
		}, (1000 / manifest.fps) * currentFrame.hold);
	}

	export function stop() {
		playing = false;
		clearInterval(interval);
		dispatch('finished');
	}

	export function reset() {
		currentFrameIndex = 0;
	}

	onDestroy(() => {
		clearInterval(interval);
	});

	// Preload frames
	onMount(() => {
		const urls = manifest.frames.map((frame) => {
			return `/animations/${manifest.name}/${frame.name}`;
		});
		loadImage(urls).then((res) => {
			images = res;

			loading = false;
			if (autoPlay) start();
		});
	});
</script>

<p>{currentFrameIndex}</p>
<img src="/animations/{manifest.name}/{currentFrame.name}" alt="test" />
<canvas class="w-full" bind:this={canvas} width="1920" height="1080" />
