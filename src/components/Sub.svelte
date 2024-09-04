<script>
	// your script goes here
	export let sub;
	import Task from './Task.svelte';
	import Link from './Link.svelte';
	import Note from './Note.svelte';
</script>

<div class="sub">
	<!-- <div class="bolge" aria-hidden="true" on:mousedown={handleMouseDown}></div> -->
	<div class="content">
		{#if sub.is == 'task'}
			<!-- <label class="label bg-lime-500">
                <input type="checkbox" checked={sub.checked} />
                {sub.text}
            </label> -->
			<Task task={sub} />
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
			<button class="editBtn bg-blue-500 px-2 h-8 flex justify-center items-center"> Edit </button>
		</div>
	</div>
</div>

<style>
	.sub {
		display: grid;
		grid-template-columns: 2rem auto; /* First column is 2rem, second adjusts based on content */
	}
</style>
