<script>
	import { onMount } from 'svelte';
	import ContextMenu from './ContextMenu.svelte';
	import Task from './Task.svelte';
	import Link from './Link.svelte';
	import Note from './Note.svelte';

	let parent;
	let parentPosition;

	let windowWidth, windowHeight;

	let selection = '';
	function handleMenuSelect(event) {
		selection = event.detail.option;

		switch (selection) {
			case 'Task':
				handleTask();
				break;

			case 'Link':
				handleLink();
				break;

			case 'Note':
				handleNote();
				break;

			default:
				break;
		}
	}

	let tasks = [],
		links = [],
		notes = [];
	function handleTask() {
		tasks = [...tasks, { text: 'Task', checked: false }];
	}

	function handleLink() {
		links = [...links, { linkName: 'google', link: 'https://www.google.com/' }];
	}

	function handleNote() {
		notes = [...notes, { text: 'Note' }];
	}

	let parentStartPositon = { x: 0, y: 0 };
	let startDragPosition, endDragPosition;
	function handleMoveBoardStart(mouse) {
		if (mouse.button !== 1) return;
		startDragPosition = { x: mouse.clientX, y: mouse.clientY };
		let x, y;
		if (parent) {
			x = parseInt(parent.style.left) || windowWidth / 2;
			y = parseInt(parent.style.top) || windowHeight / 2;
		}

		if (x && y) {
			parentStartPositon = { x: x, y: y };
		}
	}

	function handleMoveBoardEnd(mouse) {
		if (startDragPosition == undefined) return;
		endDragPosition = { x: mouse.clientX, y: mouse.clientY };

		let diffrance = {
			x: endDragPosition.x - startDragPosition.x,
			y: endDragPosition.y - startDragPosition.y
		};

		// console.log(parentStartPositon.x, parentStartPositon.y, diffrance.x, diffrance.y);

		parentPosition = `top: ${parentStartPositon.y + diffrance.y}px; left: ${parentStartPositon.x + diffrance.x}px;`;
	}

	// No need to store the parent after load, use parent directly
	onMount(() => {
		let canDrag;
		let isMiddleButton;
		windowWidth = window.innerWidth;
		windowHeight = window.innerHeight;
		// Do any additional logic if needed after mount
		document.addEventListener('mousedown', (e) => {
			isMiddleButton = e.button == 1;
			if (!isMiddleButton) return;
			e.preventDefault();
			handleMoveBoardStart(e);
			canDrag = true;
		});

		document.addEventListener('mousemove', (e) => {
			if (!canDrag) return;
			if (!isMiddleButton) return;
			e.preventDefault();
			handleMoveBoardEnd(e);
		});

		document.addEventListener('mouseup', (e) => {
			if (!isMiddleButton) return;
			e.preventDefault();
			handleMoveBoardEnd(e);
			canDrag = false;
		});
	});
</script>

<div
	bind:this={parent}
	style={parentPosition}
	class="board absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2"
>
	<ContextMenu {parent} on:select={handleMenuSelect} />

	<p class="text-gray-500 select-none">
		Right-click anywhere on the page to open the context menu.
	</p>
	{#if selection}
		<p class="text-3xl font-bold">{selection} was selected</p>
	{/if}

	{#each tasks as task}
		<Task {task} {parent} />
	{/each}

	{#each links as link}
		<Link {link} {parent} />
	{/each}

	{#each notes as note}
		<Note {note} {parent} />
	{/each}
</div>

<style>
	.board {
		width: 2000px;
		height: 2000px;
		background-color: beige;
	}
</style>
