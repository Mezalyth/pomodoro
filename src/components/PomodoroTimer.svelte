<script>
	import { onMount } from 'svelte';
  
	let time = 1500; // Default time in seconds (25 minutes)
	let timer; // Reference to the interval
	let isRunning = false; // Timer running state
	let mode = 'Work'; // Current mode
	let isExpanded = false; // Toggle for showing/hiding the timer
	let audio; // Audio element for alarm
	let workSessionsCompleted = 0; // Number of completed work sessions
  
	const WORK_DURATION = 1500; // 25 minutes
	const SHORT_BREAK_DURATION = 300; // 5 minutes
	const LONG_BREAK_DURATION = 900; // 15 minutes
  
	// Wrap document-dependent logic inside onMount to avoid SSR issues
	onMount(() => {
	  document.addEventListener('click', handleClickOutside);
  
	  // Clean up event listener on component destroy
	  return () => {
		document.removeEventListener('click', handleClickOutside);
	  };
	});
  
	// Detect click outside the tray and close it
	function handleClickOutside(event) {
	  const tray = document.querySelector('.pomodoro-container');
	  if (tray && !tray.contains(event.target) && isExpanded) {
		isExpanded = false;
	  }
	}
  
	// Timer functions
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
	  }
	}
  
	function pauseTimer() {
	  isRunning = false;
	  clearInterval(timer);
	}
  
	function stopTimer() {
	  isRunning = false;
	  clearInterval(timer);
	}
  
	function resetTimer() {
	  pauseTimer();
	  setMode(mode); // Reset to the current mode
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
	}
  
	function setMode(newMode) {
	  pauseTimer();
	  mode = newMode;
	  if (mode === 'Work') {
		time = WORK_DURATION;
	  } else if (mode === 'Short Break') {
		time = SHORT_BREAK_DURATION;
	  } else if (mode === 'Long Break') {
		time = LONG_BREAK_DURATION;
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
  
	// Load the alarm sound on mount
	onMount(() => {
	  audio = new Audio('/chimes.mp3'); // Ensure this path is correct
	  audio.addEventListener('error', () => {
		console.error('Failed to load audio file.');
	  });
	});
  
	// Format time as mm:ss
	function formatTime(seconds) {
	  const minutes = Math.floor(seconds / 60);
	  const secs = seconds % 60;
	  const formattedMinutes = minutes < 10 ? `0${minutes}` : minutes;
	  const formattedSeconds = secs < 10 ? `0${secs}` : secs;
	  return `${formattedMinutes}:${formattedSeconds}`;
	}
  </script>
  
  <!-- Toggle button with proper button element for accessibility -->
  <button 
	class="toggle-button" 
	on:click={(event) => { event.stopPropagation(); isExpanded = !isExpanded; }}
	aria-expanded={isExpanded}
	aria-label={isExpanded ? "Close Pomodoro Timer" : "Open Pomodoro Timer"}
  >
	{isExpanded ? 'Close Timer' : 'Open Timer'}
  </button>
  
  <!-- Slide-out Pomodoro Timer -->
  <div 
  class="pomodoro-container {isExpanded ? 'expanded' : 'collapsed'}" 
  role="complementary" 
  aria-hidden={!isExpanded} 
  on:click={(event) => event.stopPropagation()}
>
  <div class="pomodoro-timer">
	<h3>{mode} Time</h3>
	<div class="timer-display">{formatTime(time)}</div>

	<!-- Timer Controls - Updated to fill full width -->
	<div class="timer-controls">
	  <button on:click={startTimer} disabled={isRunning} aria-label="Start Timer">Start</button>
	  <button on:click={pauseTimer} disabled={!isRunning} aria-label="Pause Timer">Pause</button>
	  <button on:click={resetTimer} aria-label="Reset Timer">Reset</button>
	</div>

	<!-- Mode Change Buttons -->
	<div class="mode-buttons">
	  <button on:click={() => setMode('Work')} class:active-mode={mode === 'Work'}>Pomodoro (Work)</button>
	  <button on:click={() => setMode('Short Break')} class:active-mode={mode === 'Short Break'}>Short Break</button>
	  <button on:click={() => setMode('Long Break')} class:active-mode={mode === 'Long Break'}>Long Break</button>
	</div>
  </div>
</div>

<style>
  .toggle-button {
	position: fixed;
	top: 50%;
	right: 0;
	background-color: #007bff;
	color: white;
	border: none;
	padding: 10px 15px;
	cursor: pointer;
	border-radius: 5px 0 0 5px;
	z-index: 1000;
  }

  .pomodoro-container {
	position: fixed;
	top: 0;
	right: -300px; /* Hidden by default */
	width: 300px;
	height: 100%;
	background-color: #fff;
	border-left: 2px solid #ddd;
	box-shadow: -2px 0 10px rgba(0, 0, 0, 0.1);
	transition: right 0.3s ease;
	z-index: 999;
	padding: 20px;
  }

  .pomodoro-container.expanded {
	right: 0; /* Slide it in when expanded */
  }

  .pomodoro-timer {
	text-align: center;
  }

  .timer-display {
	font-size: 2.5rem;
	margin-bottom: 1rem;
  }

  /* Timer Controls */
  .timer-controls {
	display: flex;
	justify-content: space-between; /* Spread buttons evenly */
	gap: 10px; /* Optional gap between buttons */
	margin-bottom: 20px;
  }

  .timer-controls button {
	flex: 1; /* Make buttons take equal space */
	padding: 8px 12px;
	font-size: 1.2rem;
	cursor: pointer;
	border: none;
	background-color: #007bff;
	color: white;
	border-radius: 5px;
	display: inline-flex;
	justify-content: center;
	align-items: center;
  }

  .timer-controls button:disabled {
	opacity: 0.5;
	cursor: not-allowed;
  }

  .timer-controls button:hover:not(:disabled) {
	background-color: #0056b3;
  }

  /* Vertically stacked mode buttons */
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
  }

  .mode-buttons button.active-mode {
	background-color: #007bff;
	color: white;
	border-color: #007bff;
  }

  .mode-buttons button:hover {
	background-color: #f0f0f0;
  }

  .mode-buttons button.active-mode:hover {
	background-color: #0056b3;
	border-color: #0056b3;
  }
</style>
