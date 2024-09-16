<script>
	export let wordCount = 0;
	let goal = 50000; // Default word goal

	function setNewGoal() {
		const newGoal = prompt('Set new word count goal:', goal);
		if (newGoal && !isNaN(newGoal)) {
			goal = parseInt(newGoal);
		}
	}

	// Calculate the percentage of the goal that has been completed
	$: progress = Math.min((wordCount / goal) * 100, 100);
</script>

<div>
	<h3>Word Goal Progress</h3>
	<p>{wordCount}/{goal} words ({progress.toFixed(2)}%)</p>

	<!-- Progress Bar -->
	<progress value={progress} max="100"></progress>

	<!-- Button to set a new goal -->
	<button on:click={setNewGoal}>Change Goal</button>
</div>
