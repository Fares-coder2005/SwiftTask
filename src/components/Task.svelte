<script>
	import { onMount, onDestroy } from 'svelte';
	import Sub from './Sub.svelte';

	export let parent;
	export let task;
	let text = task.text;
	let checked = task.checked;

	onMount(() => {
		console.log(parent);
	});
</script>

<details>
	<summary
		><label class="label bg-lime-500">
			<input type="checkbox" bind:checked />
			{text}
		</label></summary
	>
	Sx <br />
	Sx <br />
	Sx <br />
	Sx <br />
	Sx <br />
	Sx <br />
	Sx <br />
	Sx <br />

	Sx <br />
</details>

<div class="subs translate-x-8">
	{#if task.subs && task.subs.length > 0}
		<!-- content here -->
		{#each task.subs as sub}
			<Sub {sub} />
			<!-- <div class="sub">
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
		</div> -->
		{/each}
	{/if}
</div>

<style>
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
