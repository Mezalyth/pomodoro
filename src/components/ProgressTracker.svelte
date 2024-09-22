<!-- ProgressTracker.svelte -->
<script>
	export let wordCount = 0;
	let goal = 50000; // Default word goal
	let progress = 0;
  
	// Compute progress percentage
	$: progress = goal > 0 ? (wordCount / goal) * 100 : 0;
  
	// Format progress to have at most two decimal places
	$: formattedProgress = progress.toFixed(2);
</script>

<div class="progress-tracker">
	<h3>Word Goal Progress</h3>
	<!-- Word count display combined with goal input -->
	<p>
		{wordCount} / 
		<input
			id="goalInput"
			type="number"
			min="1"
			bind:value={goal}
			class="goal-input"
			aria-label="Set Word Goal"
		/>
		words ({formattedProgress}%)
	</p>
  
	<!-- Progress Bar -->
	<progress
		value={Math.min(progress, 100)}
		max="100"
		aria-label="Progress towards word goal"
	></progress>
</div>

<style>
	.progress-tracker {
		margin-top: 20px;
	}

	.progress-tracker h3 {
		margin-bottom: 10px;
	}

	.progress-tracker p {
		margin-bottom: 10px;
	}

	progress {
		width: 100%;
		height: 20px;
		margin-bottom: 10px;
	}

	.goal-input {
		width: 80px;
		padding: 5px;
		margin-left: 5px;
		box-sizing: border-box;
		display: inline-block;
		font-size: 1rem;
		text-align: right;
		border: 1px solid #ccc;
		border-radius: 5px;
	}
</style>
