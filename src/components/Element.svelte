<script>
	import { onDestroy, onMount } from 'svelte';
	import Task from './Task.svelte';
	import Note from './Note.svelte';
	import Link from './Link.svelte';

	export let prop;
	export let parent;

	let canDrag = false;
	let elmnt;
	let startDragPosition = { x: 0, y: 0 };
	let initialPosition = prop.coordinates || { x: 0, y: 0 };

	function handleMouseDown(e) {
		if (e.button !== 0) return;
		canDrag = true;
		startDragPosition = { x: e.clientX, y: e.clientY };
		initialPosition = { x: elmnt.offsetLeft, y: elmnt.offsetTop };
		e.preventDefault(); // Prevent text selection
	}

	let getItem;
	if (prop.is == 'task') {
		getItem = 'Tasks';
	} else if (prop.is == 'link') {
		getItem = 'Links';
	} else if (prop.is == 'note') {
		getItem = 'Notes';
	}
	function handleMouseUp() {
		canDrag = false;

		// Get the updated coordinates
		const xCoordinate = parseFloat(elmnt.style.left);
		const yCoordinate = parseFloat(elmnt.style.top);

		// Retrieve the tasks from localStorage
		let storedTasks = JSON.parse(localStorage.getItem(getItem)) || [];

		// Find the task in the stored tasks array and update its coordinates
		storedTasks = storedTasks.map((storedTask) => {
			if (storedTask.id === prop.id) {
				return {
					...storedTask,
					coordinates: { x: xCoordinate, y: yCoordinate }
				};
			}
			return storedTask;
		});

		// Save the updated tasks back to localStorage
		localStorage.setItem(getItem, JSON.stringify(storedTasks));
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

	// function handleDelete() {
	// 	console.log(getItem, prop.id);
	// }

	function handleDelete() {
		// Get the items (Tasks, Links, Notes) from localStorage based on prop type
		let storedItems = JSON.parse(localStorage.getItem(getItem)) || [];

		// Filter out the item to delete
		storedItems = storedItems.filter((item) => item.id !== prop.id);

		// Update localStorage with the remaining items
		localStorage.setItem(getItem, JSON.stringify(storedItems));

		// Optionally inform the parent that the item has been deleted
		// if a callback was provided (assuming parent wants to know)
		if (typeof parent?.onDelete === 'function') {
			parent.onDelete(prop.id);
		}

		console.log(`Deleted ${getItem} with ID: ${prop.id}`);
	}

	onMount(() => {
		console.log(parent);

		if (prop.coordinates) {
			elmnt.style.left = `${prop.coordinates.x}px`;
			elmnt.style.top = `${prop.coordinates.y}px`;
		}
		document.addEventListener('mouseup', handleMouseUp);
		document.addEventListener('mousemove', handleMouseMove);
	});

	onDestroy(() => {
		document.removeEventListener('mouseup', handleMouseUp);
		document.removeEventListener('mousemove', handleMouseMove);
	});
</script>

<!-- <p>{JSON.stringify(prop)}</p> -->
<div bind:this={elmnt} class="elmnt">
	<div class="bolge" aria-hidden="true" on:mousedown={handleMouseDown}></div>
	<div class="content">
		{#if prop.is == 'task'}
			<Task task={prop} {parent} />
		{:else if prop.is == 'link'}
			<Link link={prop} {parent} />
		{:else if prop.is == 'note'}
			<Note note={prop} {parent} />
		{/if}
		<div class="btns flex justify-center items-center flex-row">
			<button
				on:click={handleDelete}
				class="deleteBtn bg-red-500 px-2 h-8 flex justify-center items-center"
			>
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
