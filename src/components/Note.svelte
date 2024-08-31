<script>
	import { onDestroy, onMount } from 'svelte';

	export let note;
	export let parent;

	let elmnt;
	let canDrag = false;
	let startDragPosition = { x: 0, y: 0 };
	let initialPosition = { x: 0, y: 0 };

	function handleMouseDown(e) {
		if (e.button !== 0) return;
		canDrag = true;
		startDragPosition = { x: e.clientX, y: e.clientY };
		initialPosition = { x: elmnt.offsetLeft, y: elmnt.offsetTop };
		e.preventDefault(); // Prevent text selection
	}

	function handleMouseUp() {
		canDrag = false;
	}

	function handleMouseMove(e) {
		if (!canDrag) return;

		const diffrance = {
			x: e.clientX - startDragPosition.x,
			y: e.clientY - startDragPosition.y
		};

		if (parent) {
			elmnt.style.left = `${Math.max(0, Math.min(parseFloat(window.getComputedStyle(parent).width), initialPosition.x + diffrance.x))}px`;
			elmnt.style.top = `${Math.max(0, Math.min(parseFloat(window.getComputedStyle(parent).height), initialPosition.y + diffrance.y))}px`;
		}

		// console.log(elmnt.style.left, elmnt.style.top);
	}

	onMount(() => {
		document.addEventListener('mouseup', handleMouseUp);
		document.addEventListener('mousemove', handleMouseMove);
	});

	onDestroy(() => {
		document.removeEventListener('mouseup', handleMouseUp);
		document.removeEventListener('mousemove', handleMouseMove);
	});
</script>

<div bind:this={elmnt} class="elmnt">
	<div class="bolge" aria-hidden="true" on:mousedown={handleMouseDown}></div>
	<p>{note.text}</p>
</div>

<style>
	.elmnt {
		position: absolute;
		display: flex;
		justify-content: center;
		align-items: center;
		cursor: grab;
	}

	p {
		display: flex;
		justify-content: center;
		align-items: center;
		width: 12rem;
		height: 2rem;
		font-size: 1rem;
		border: 1px solid black;
		user-select: none;
	}

	.bolge {
		height: 2rem;
		aspect-ratio: 1/1;
		background-color: black;
	}
</style>
