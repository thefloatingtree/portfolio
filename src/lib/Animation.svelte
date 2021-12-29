<script>
	import { createEventDispatcher, onDestroy, onMount } from 'svelte';
	import loadImage from 'image-promise';

	export let manifest;
	export let playing = false;
	export let loading = true;

	export let autoPlay = false;
	export let loop = false;

	const dispatch = createEventDispatcher();

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
		loadImage(urls).then(() => {
			loading = false;
			if (autoPlay) start();
		});
	});
</script>

<p>{currentFrameIndex}</p>
<img src="/animations/{manifest.name}/{currentFrame.name}" alt="test" />
