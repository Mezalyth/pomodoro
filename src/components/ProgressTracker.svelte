<script>
	export let wordCount = 0;
	let goal = 1667; // Default word goal
	let progress = 0;
  
	// Compute progress percentage
	$: progress = goal > 0 ? (wordCount / goal) * 100 : 0;
  
	// Format progress to have at most two decimal places
	$: formattedProgress = progress.toFixed(2);
</script>

<div class="progress-tracker">
	<div class="progress-header">
		<h3>Sprint Progress</h3>
		<!-- Word count display combined with goal input, right-aligned -->
		<p>
			{wordCount}/
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
	</div>
  
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

	.progress-header {
		display: flex;
		justify-content: space-between; /* Pushes h3 and p to opposite sides */
		align-items: center;
	}

	.progress-tracker h3 {
		margin: 0;
	}

	.progress-tracker p {
		margin: 0;
	}

	progress {
		width: 100%;
		height: 20px;
		margin-top: 10px;
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

	progress {
	width: 100%;
	height: 8px;
	margin: 10px 0 10px 0;
	appearance: none; /* Remove default styling */
	}

	progress::-webkit-progress-bar {
		background-color: #f3f3f3; /* Background of the bar */
		border-radius: 10px; /* Optional rounded corners */
	}

	progress::-webkit-progress-value {
		background-color: #37699e; /* Progress fill color */
		border-radius: 10px; /* Optional rounded corners */
	}

	progress::-moz-progress-bar {
		background-color: #37699e; /* Progress fill color for Firefox */
		border-radius: 10px; /* Optional rounded corners */
	}

</style>
