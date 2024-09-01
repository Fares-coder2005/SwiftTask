<script>
	import { onDestroy, onMount } from 'svelte';

	export let group;
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
	<div class="content">
		<div class="border-1 border-black min-w-52 w-fit min-h-56 bg-lime-500">
			<p class="flex justify-center items-center w-64 m-2">
				{group.name}
			</p>
		</div>
		<div class="btns flex justify-center items-center flex-row">
			<button class="deleteBtn bg-red-500 px-2 h-8 flex justify-center items-center">
				Delete
			</button>
			<button class="editBtn bg-blue-500 px-2 h-8 flex justify-center items-center"> Edit </button>
		</div>
	</div>
</div>

<style>
	.elmnt {
		position: absolute;
		display: grid;
		grid-template-columns: 2rem auto; /* First column is 2rem, second adjusts based on content */
		/* gap: 1rem; Optional: Adds space between the two sections */
	}
	.bolge {
		height: 100%;
		background-color: black;
		cursor: grab;
	}

	.content {
		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: row;
		border: 1px solid black;
	}
</style>
