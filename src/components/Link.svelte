<script>
	import { onDestroy, onMount } from 'svelte';

	// export let urlName;
	// export let url;
	export let link;
	export let parent;
</script>

<a class="bg-lime-500" target="_blank" href={link.link}>
	{link.linkName}
</a>

<style>
	a {
		display: flex;
		justify-content: center;
		align-items: center;
		width: 12rem;
		height: 2rem;
		font-size: 1rem;
		user-select: none;
	}
</style>
