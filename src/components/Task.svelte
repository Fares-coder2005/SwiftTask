<script>
	import { onMount, onDestroy } from 'svelte';

	export let parent;
	export let task;
	let text = task.text;
	let checked = task.checked;

	let canDrag = false;
	let elmnt;
	let startDragPosition = { x: 0, y: 0 };
	let initialPosition = task.coordinates || { x: 0, y: 0 };

	function handleMouseDown(e) {
		if (e.button !== 0) return;
		canDrag = true;
		startDragPosition = { x: e.clientX, y: e.clientY };
		initialPosition = { x: elmnt.offsetLeft, y: elmnt.offsetTop };
		e.preventDefault(); // Prevent text selection
	}

	function handleMouseUp() {
		canDrag = false;

		// Get the updated coordinates
		const xCoordinate = parseFloat(elmnt.style.left);
		const yCoordinate = parseFloat(elmnt.style.top);

		// Retrieve the tasks from localStorage
		let storedTasks = JSON.parse(localStorage.getItem('Tasks')) || [];

		// Find the task in the stored tasks array and update its coordinates
		storedTasks = storedTasks.map((storedTask) => {
			if (storedTask.id === task.id) {
				return {
					...storedTask,
					coordinates: { x: xCoordinate, y: yCoordinate }
				};
			}
			return storedTask;
		});

		// Save the updated tasks back to localStorage
		localStorage.setItem('Tasks', JSON.stringify(storedTasks));
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

	// function handleDelete(params) {
	// 	handle;
	// }

	onMount(() => {
		elmnt.style.left = `${task.coordinates.x}px`;
		elmnt.style.top = `${task.coordinates.y}px`;
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
		<label class="label bg-lime-500">
			<input type="checkbox" bind:checked />
			{text}
		</label>

		<div class="btns flex justify-center items-center flex-row">
			<button class="deleteBtn bg-red-500 px-2 h-8 flex justify-center items-center">
				Delete
			</button>
			<button class="editBtn bg-blue-500 px-2 h-8 flex justify-center items-center"> Edit </button>
		</div>
	</div>
	<div class="subs translate-x-8">
		{#each task.subs as sub}
			<div class="sub">
				<div class="bolge" aria-hidden="true" on:mousedown={handleMouseDown}></div>
				<div class="content">
					{#if sub.is == 'task'}
						<label class="label bg-lime-500">
							<input type="checkbox" checked={sub.checked} />
							{sub.text}
						</label>
					{:else if sub.is == 'link'}
						<a class="label bg-lime-500" target="_blank" href={sub.link}>
							{sub.linkName}
						</a>
					{:else if sub.is == 'note'}
						<p class="label bg-lime-500">{sub.text}</p>
					{/if}
					<div class="btns flex justify-center items-center flex-row">
						<button class="deleteBtn bg-red-500 px-2 h-8 flex justify-center items-center">
							Delete
						</button>
						<button class="editBtn bg-blue-500 px-2 h-8 flex justify-center items-center">
							Edit
						</button>
					</div>
				</div>
			</div>
		{/each}
	</div>
</div>

<style>
	.elmnt {
		position: absolute;
		display: grid;
		grid-template-columns: 2rem auto; /* First column is 2rem, second adjusts based on content */
		/* gap: 1rem; Optional: Adds space between the two sections */
	}

	.sub {
		display: grid;
		grid-template-columns: 2rem auto; /* First column is 2rem, second adjusts based on content */
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

	.label {
		display: flex;
		justify-content: center;
		align-items: center;
		width: 12rem;
		height: 2rem;
		font-size: 1rem;
		user-select: none;
	}

	input {
		width: 1rem;
		height: 1rem;
	}
</style>
