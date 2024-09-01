<script>
	import { onMount } from 'svelte';
	import { createEventDispatcher } from 'svelte';

	let showMenu = false;
	let menuStyle = '';
	let Menu;
	export let parent; // Accept the parent element as a prop

	const dispatcher = createEventDispatcher();

	function openContextMenu(event) {
		event.preventDefault();
		if (event.target !== parent) return;

		var rect = event.target.getBoundingClientRect();
		var x = event.clientX - rect.left; //x position within the element.
		var y = event.clientY - rect.top; //y position within the element.
		// console.log('Left: ' + x + ' ; Top: ' + y + '.');

		showMenu = true;
		menuStyle = `top: ${y}px; left: ${x}px;`;
		document.addEventListener('click', handleClickOutside);
	}

	function handleSelection(option) {
		dispatcher('select', { option });
		closeContextMenu();
	}

	function closeContextMenu() {
		showMenu = false;
		document.removeEventListener('click', handleClickOutside);
	}

	function handleClickOutside(event) {
		if (!event.target.closest('.context-menu')) {
			closeContextMenu();
		}
	}

	onMount(() => {
		// Ensure the parent is available and add the contextmenu listener
		document.addEventListener('contextmenu', openContextMenu);
	});
</script>

{#if showMenu}
	<div
		class="context-menu absolute z-50 bg-white border border-gray-200 shadow-md rounded"
		style={menuStyle}
		bind:this={Menu}
	>
		<ul class="list-none p-0 m-0">
			<li class="text-l hover:bg-gray-100 cursor-pointer">
				<button class=" p-3 w-full h-full" on:click={() => handleSelection('Task')}> Task </button>
			</li>
			<li class="text-l hover:bg-gray-100 cursor-pointer">
				<button class=" p-3 w-full h-full" on:click={() => handleSelection('Link')}> Link </button>
			</li>
			<li class="text-l hover:bg-gray-100 cursor-pointer">
				<button class=" p-3 w-full h-full" on:click={() => handleSelection('Note')}> Note </button>
			</li>
			<li class="text-l hover:bg-gray-100 cursor-pointer">
				<button class=" p-3 w-full h-full" on:click={() => handleSelection('Group')}>
					Group
				</button>
			</li>
		</ul>
	</div>
{/if}
