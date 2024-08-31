<script>
	import { onMount } from 'svelte';
	import Board from '../components/Board.svelte';

	onMount(() => {
		let isCtrlPressed = false;

		// Listen for keydown and keyup to track if Ctrl is pressed
		document.addEventListener('keydown', (event) => {
			if (event.key === 'Control') {
				isCtrlPressed = true;
			}
		});

		document.addEventListener('keyup', (event) => {
			if (event.key === 'Control') {
				isCtrlPressed = false;
			}
		});

		// Listen for the mousewheel event to prevent zooming
		document.addEventListener(
			'wheel',
			(event) => {
				if (isCtrlPressed) {
					event.preventDefault(); // Disable zooming
					// console.log('Zooming with Ctrl + mousewheel disabled');
				}
			},
			{ passive: false }
		); // `passive: false` is needed to call `preventDefault()`
	});
</script>

<Board />
