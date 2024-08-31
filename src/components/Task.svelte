<script>
	import { onMount, onDestroy } from 'svelte';

	export let parent;
	export let task;
	let text = task.text;
	let checked = task.checked;

	let canDrag = false;
	let elmnt;
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
	<label class="label">
		<input type="checkbox" bind:checked />
		{text}
	</label>
</div>

<style>
	.elmnt {
		position: absolute;
		display: flex;
		justify-content: center;
		align-items: center;
		cursor: grab;
	}

	.label {
		display: flex;
		justify-content: center;
		align-items: center;
		width: 12rem;
		height: 2rem;
		font-size: 1rem;
		border: 1px solid black;
		user-select: none;
	}

	input {
		width: 1rem;
		height: 1rem;
	}

	.bolge {
		height: 2rem;
		aspect-ratio: 1/1;
		background-color: black;
	}
</style>
