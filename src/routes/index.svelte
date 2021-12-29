<script>
	import Animation from '$lib/Animation.svelte';
	import horseAnimationManifest from '/static/animations/horse/manifest.json';

	let animation;
</script>

<button
	class="px-6 py-3 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition-all"
	on:click={() => {
		animation.start();
	}}>Start</button
>
<Animation bind:this={animation} manifest={horseAnimationManifest} />
