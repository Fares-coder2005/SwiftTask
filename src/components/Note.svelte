<script>
	import { onDestroy, onMount } from 'svelte';

	export let note;
	export let parent;
</script>

<p class="bg-lime-500">{note.text}</p>

<style>
	p {
		display: flex;
		justify-content: center;
		align-items: center;
		width: 12rem;
		height: 2rem;
		font-size: 1rem;
		/* user-select: none; */
	}
</style>
