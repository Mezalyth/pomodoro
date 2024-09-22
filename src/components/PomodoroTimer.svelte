<!-- PomodoroTimer.svelte -->
<script>
	import { onMount, onDestroy } from 'svelte';
	
	// Timer State Variables
	let time = 1500; // Default time in seconds (25 minutes)
	let timer; // Reference to the interval
	let isRunning = false; // Timer running state
	let mode = 'Work'; // Current mode
	let isExpanded = false; // Toggle for showing/hiding the timer
	let isHovered = false; // Hover state for peek functionality
	let audio; // Audio element for alarm
	let workSessionsCompleted = 0; // Number of completed work sessions
	
	// Constants for Timer Durations
	const WORK_DURATION = 1500; // 25 minutes
	const SHORT_BREAK_DURATION = 300; // 5 minutes
	const LONG_BREAK_DURATION = 900; // 15 minutes
	
	// Threshold for mouse proximity (in pixels)
	const PEAK_THRESHOLD = 200; // Adjust as needed
	
	// Function to get duration based on mode
	function getDurationForMode(currentMode) {
	  if (currentMode === 'Work') return WORK_DURATION;
	  if (currentMode === 'Short Break') return SHORT_BREAK_DURATION;
	  if (currentMode === 'Long Break') return LONG_BREAK_DURATION;
	  return WORK_DURATION; // Default fallback
	}
	
	// Function to handle mouse movement for peek behavior
	function handleMouseMove(event) {
	  const screenWidth = window.innerWidth;
	  if (event.clientX > screenWidth - PEAK_THRESHOLD && !isExpanded) {
		isHovered = true;
	  } else {
		isHovered = false;
	  }
	}
	
	// Function to handle click outside the tray and close it
	function handleClickOutside(event) {
	  const tray = document.querySelector('.pomodoro-container');
	  if (tray && !tray.contains(event.target) && isExpanded) {
		isExpanded = false;
	  }
	}
	
	// Timer Control Functions
	function startTimer() {
	  if (!isRunning) {
		isRunning = true;
		clearInterval(timer);
		timer = setInterval(() => {
		  if (time > 0) {
			time -= 1;
		  } else {
			playSound();
			stopTimer();
			switchToNextSession(); // Automatically switch to the next session when time is up
		  }
		}, 1000);
		console.log('Timer started.');
	  }
	}
	
	function pauseTimer() {
	  if (isRunning) {
		isRunning = false;
		clearInterval(timer);
		console.log('Timer paused.');
	  }
	}
	
	function stopTimer() {
	  isRunning = false;
	  clearInterval(timer);
	  console.log('Timer stopped.');
	}
	
	function resetTimer() {
	  pauseTimer();
	  setMode('Work'); // Reset to the first Pomodoro/work session
	  console.log('Timer reset.');
	}
	
	function switchToNextSession() {
	  if (mode === 'Work') {
		workSessionsCompleted += 1; // Track how many work sessions have been completed
		if (workSessionsCompleted % 4 === 0) {
		  setMode('Long Break'); // After 4 work sessions, take a long break
		} else {
		  setMode('Short Break'); // Otherwise, take a short break
		}
	  } else {
		setMode('Work'); // After any break, go back to work
	  }
	  console.log(`Switched to next session: ${mode}`);
	}
	
	function setMode(newMode) {
	  pauseTimer();
	  mode = newMode;
	  time = getDurationForMode(mode);
	  console.log(`Mode set to: ${mode}`);
	}
	
	// Function to toggle between starting and pausing the timer
	function toggleTimer() {
	  if (isRunning) {
		pauseTimer();
	  } else {
		startTimer();
	  }
	}
	
	// Play alarm sound
	function playSound() {
	  if (audio) {
		audio.currentTime = 0; // Reset audio to the start before playing
		audio.play().catch(error => {
		  console.error('Audio playback failed:', error);
		});
	  }
	}
	
	// Format time as mm:ss
	function formatTime(seconds) {
	  const minutes = Math.floor(seconds / 60);
	  const secs = seconds % 60;
	  const formattedMinutes = minutes < 10 ? `0${minutes}` : minutes;
	  const formattedSeconds = secs < 10 ? `0${secs}` : secs;
	  return `${formattedMinutes}:${formattedSeconds}`;
	}
	
	// Initialize components and event listeners on mount
	onMount(() => {
	  // Initialize audio
	  audio = new Audio('/chimes.mp3'); // Ensure this path is correct
	  audio.addEventListener('error', () => {
		console.error('Failed to load audio file.');
	  });
	
	  // Add event listeners for mouse movement and outside clicks
	  document.addEventListener('mousemove', handleMouseMove);
	  document.addEventListener('click', handleClickOutside);
	
	  // Cleanup on component destroy
	  return () => {
		document.removeEventListener('mousemove', handleMouseMove);
		document.removeEventListener('click', handleClickOutside);
		clearInterval(timer); // Clear the timer interval when unmounted
		if (audio) {
		  audio.pause();
		  audio.src = '';
		}
	  };
	});
	
	// Ensure timer is cleared on component destroy
	onDestroy(() => {
	  clearInterval(timer);
	});
</script>

<!-- Slide-out Pomodoro Timer -->
<div 
	class="pomodoro-container {isExpanded ? 'expanded' : ''} {isHovered && !isExpanded ? 'peek' : ''}" 
	role="complementary" 
	aria-hidden={!isExpanded} 
	on:click={(event) => event.stopPropagation()} 
>
	<div class="pomodoro-timer">
	  <h3>{mode} Time</h3>
	  <div class="timer-display">{formatTime(time)}</div>
	  <!-- Mode Change Buttons -->
	  <div class="mode-buttons">
		<button on:click={() => setMode('Work')} class:active-mode={mode === 'Work'}>Pomodoro (Work)</button>
		<button on:click={() => setMode('Short Break')} class:active-mode={mode === 'Short Break'}>Short Break</button>
		<button on:click={() => setMode('Long Break')} class:active-mode={mode === 'Long Break'}>Long Break</button>
	  </div>
	  <!-- Timer Controls -->
	  <div class="timer-controls">
		<button on:click={toggleTimer} aria-label={isRunning ? "Pause Timer" : "Start Timer"}>
		  {isRunning ? 'Pause' : 'Start'}
		</button>
		<button on:click={resetTimer} aria-label="Reset Timer">Reset</button>
	  </div>
	</div>
</div>

<!-- Toggle button stuck to the side of the tray (vertically oriented) -->
<button 
	class="toggle-button {isExpanded ? 'expanded-button' : ''}"
	on:click={(event) => { event.stopPropagation(); isExpanded = !isExpanded; }}
	aria-expanded={isExpanded}
	aria-label={isExpanded ? "Close Pomodoro Timer" : "Open Pomodoro Timer"}
>
	{isExpanded ? 'Close Timer' : 'Open Timer'}
</button>

<style>
	/* Pomodoro Timer Styles */
	.pomodoro-container {
	  position: fixed;
	  top: 0;
	  right: -320px; /* Hidden by default */
	  width: 280px;
	  height: 100%;
      background: #f1f1f1;
	  box-shadow: -2px 0 10px rgba(0, 0, 0, 0.1);
	  transition: right 0.3s ease;
	  z-index: 999;
	  padding: 20px;
	  overflow-y: auto; /* Add scroll if content overflows */
	}

	.pomodoro-container.expanded {
	  right: 0; /* Slide it in when expanded */
	}

	.pomodoro-container.peek {
	  right: -270px; /* Partially peek out */
	}

	.pomodoro-timer {
	  text-align: center;
	}

	.timer-display {
	  font-size: 2.5rem;
	  margin-bottom: 1rem;
	}

	/* Mode Buttons */
	.mode-buttons {
	  display: flex;
	  flex-direction: column;
	  align-items: center;
	  width: 100%;
	}

	.mode-buttons button {
	  width: 100%;
	  padding: 12px;
	  font-size: 1rem;
	  margin-bottom: 10px;
	  cursor: pointer;
	  border: 1px solid #333;
	  background-color: #fff;
	  border-radius: 5px;
	  transition: background-color 0.2s ease, border-color 0.2s ease;
	}

	.mode-buttons button.active-mode {
	  background-color: #37699e;
	  color: white;
	}

	.mode-buttons button:hover {
	  background-color: #f0f0f0;
	}

	.mode-buttons button.active-mode:hover {
	  background-color: #0056b3;
	}

	/* Timer Controls */
	.timer-controls {
	  display: flex;
	  justify-content: space-between;
	  gap: 10px;
	  margin-top: 20px;
	}

	.timer-controls button {
	  flex: 2 2 66%; /* Make this button take up two-thirds */
	  padding: 8px 12px;
	  font-size: 1.2rem;
	  cursor: pointer;
	  border: none;
	  background-color: #37699e;
	  color: white;
	  border-radius: 5px;
	  display: inline-flex;
	  justify-content: center;
	  align-items: center;
	  transition: background-color 0.2s ease;
	}

	.timer-controls button:last-child {
	  flex: 1 1 33%; /* Make this button take up one-third */
	}

	.timer-controls button:disabled {
	  opacity: 0.5;
	  cursor: not-allowed;
	}

	.timer-controls button:hover:not(:disabled) {
	  background-color: #0056b3;
	}

	/* Toggle Button Styles */
	.toggle-button {
	  position: fixed;
	  top: 30%;
	  right: 40px; /* Adjusted for right side */
	  background-color: #37699e;
	  color: white;
	  border: none;
	  padding: 16px 50px;
	  cursor: pointer;
	  border-radius: 5px;
	  z-index: 1000;
	  transform: rotate(-90deg); /* Rotated -90 degrees for vertical text on right side */
	  transform-origin: right center;
	  transition: right 0.3s ease, transform 0.3s ease;
	}
	
	/* Button moves with the tray */
	.expanded-button {
	  right: 340px; /* Moves out with the tray when expanded */
	}
	
	.toggle-button:hover {
	  background-color: #0056b3;
	}

	/* Responsive Design */
	@media (max-width: 600px) {
	  .pomodoro-container {
		width: 80%;
		right: -80%;
	  }

	  .pomodoro-container.expanded {
		right: 0;
	  }

	  .pomodoro-container.peek {
		right: -70%;
	  }

	  .toggle-button {
		right: 20px;
		padding: 12px 30px;
		font-size: 0.9rem;
	  }

	  .toggle-button.expanded-button {
		right: 80%;
	  }
	}
</style>
