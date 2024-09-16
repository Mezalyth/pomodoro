<script>
	export let content = '';
	export let updateWordCount;

	function handleInput(event) {
		content = event.target.value;
		updateWordCount(content);
	}
</script>

<textarea
	bind:value={content}
	on:input={handleInput}
	rows="15"
	cols="80"
	placeholder="Start writing..."
></textarea>
