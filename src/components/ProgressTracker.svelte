<script>
	export let wordCount = 0;
	let goal = 50000;            // Default word goal
	let showGoalInput = false;   // Show or hide the goal input modal
	let newGoalValue = goal;     // Temporary storage for new goal input
  
	function openGoalInput() {
	  newGoalValue = goal;
	  showGoalInput = true;
	}
  
	function updateGoal() {
	  const parsedGoal = parseInt(newGoalValue, 10);
	  if (Number.isFinite(parsedGoal) && parsedGoal > 0) {
		goal = parsedGoal;
		showGoalInput = false;
	  } else {
		alert('Please enter a valid positive number.');
	  }
	}
  
	// Calculate the percentage of the goal that has been completed
	$: progress = goal > 0 ? (wordCount / goal) * 100 : 0;
  </script>
  
  <div class="progress-tracker">
	<h3>Word Goal Progress</h3>
	<p>{wordCount}/{goal} words ({progress.toFixed(2)}%)</p>
  
	<!-- Progress Bar -->
	<progress
	  value={Math.min(progress, 100)}
	  max="100"
	  aria-label="Progress towards word goal"
	></progress>
  
	<!-- Button to set a new goal -->
	<button on:click={openGoalInput}>Change Goal</button>
  
	{#if showGoalInput}
	  <div
		class="modal-overlay"
		role="dialog"
		aria-modal="true"
		on:click={() => (showGoalInput = false)}
	  >
		<div class="modal" on:click|stopPropagation>
		  <h4>Set New Word Goal</h4>
		  <label for="goalInput">New Goal:</label>
		  <input
			id="goalInput"
			type="number"
			min="1"
			bind:value={newGoalValue}
		  />
		  <div class="modal-buttons">
			<button on:click={updateGoal}>Save</button>
			<button on:click={() => (showGoalInput = false)}>Cancel</button>
		  </div>
		</div>
	  </div>
	{/if}
  </div>
  
  <style>
	.progress-tracker {
	  margin-top: 20px;
	}
  
	progress {
	  width: 100%;
	  height: 20px;
	  margin-bottom: 10px;
	}
  
	/* Modal Styles */
	.modal-overlay {
	  position: fixed;
	  top: 0;
	  left: 0;
	  right: 0;
	  bottom: 0;
	  background: rgba(0, 0, 0, 0.5);
	  display: flex;
	  justify-content: center;
	  align-items: center;
	  z-index: 1000;
	}
  
	.modal {
	  background: #fff;
	  padding: 20px;
	  border-radius: 5px;
	  width: 300px;
	}
  
	.modal h4 {
	  margin-top: 0;
	}
  
	.modal label {
	  display: block;
	  margin-bottom: 8px;
	}
  
	.modal input {
	  width: 100%;
	  padding: 8px;
	  margin-bottom: 12px;
	  box-sizing: border-box;
	  border: 1px solid #ccc;
	  border-radius: 5px;
	}
  
	.modal-buttons {
	  text-align: right;
	}
  
	.modal-buttons button {
	  padding: 8px 12px;
	  font-size: 1rem;
	  margin-left: 5px;
	  cursor: pointer;
	  border: 1px solid #333;
	  background-color: #fff;
	  border-radius: 5px;
	}
  
	.modal-buttons button:hover {
	  background-color: #f0f0f0;
	}
  </style>
  