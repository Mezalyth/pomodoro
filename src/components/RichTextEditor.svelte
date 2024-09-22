<script>
	import { onMount } from 'svelte';
	import { browser } from '$app/environment';
	import { createEventDispatcher } from 'svelte';

	export let content = ''; // Bound to Quill editor content
	let quill;
	let editorDiv;
	const dispatch = createEventDispatcher(); // Svelte event dispatcher

	onMount(async () => {
		if (browser) {
			try {
				const Quill = (await import('quill')).default;

				quill = new Quill(editorDiv, {
					theme: 'snow',
					modules: {
						toolbar: [
							[{ header: [1, 2, 3, 4, 5, 6, false] }],
							['bold', 'italic', 'underline', 'strike'],
							[{ indent: '-1' }, { indent: '+1' }],
							[{ color: [] }, { background: [] }],
							[{ font: [] }],
							[{ align: [] }],
							['clean']
						]
					}
				});

				const savedContent = localStorage.getItem('quillContent');
				if (savedContent) {
					quill.clipboard.dangerouslyPasteHTML(savedContent);
					content = savedContent;
				}

				quill.on('text-change', () => {
					content = quill.root.innerHTML;
					localStorage.setItem('quillContent', content);
					dispatch('typing', true); // Trigger typing event
				});
			} catch (error) {
				console.error('Error loading Quill:', error);
			}
		}
	});

	function stopTyping() {
		dispatch('typing', false); // Trigger typing stopped event
	}

	// Optionally, detect when user stops typing
	let typingTimeout;
	quill?.on('text-change', () => {
		if (typingTimeout) clearTimeout(typingTimeout);
		typingTimeout = setTimeout(stopTyping, 2000); // Stop typing after 2 seconds of inactivity
	});
</script>

<div bind:this={editorDiv}></div>

<style global>
	@import 'quill/dist/quill.snow.css';

	.ql-editor {
		min-height: 200px;
		background: #f1f1f1;
	}
</style>
