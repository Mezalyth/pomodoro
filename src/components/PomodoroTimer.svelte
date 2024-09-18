<script>
	import { onMount, onDestroy } from 'svelte';
  
	let time = 1500;         // Default time in seconds (25 minutes)
	let timer;               // Reference to the interval
	let isRunning = false;   // Timer running state
	let mode = 'Work';       // Current mode
	let audio;               // Audio element for alarm
  
	// Durations for different modes
	const WORK_DURATION = 1500;          // 25 minutes
	const SHORT_BREAK_DURATION = 300;    // 5 minutes
	const LONG_BREAK_DURATION = 900;     // 15 minutes
  
	// Start the timer
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
		  }
		}, 1000);
	  }
	}
  
	// Pause the timer
	function pauseTimer() {
	  isRunning = false;
	  clearInterval(timer);
	}
  
	// Stop the timer completely
	function stopTimer() {
	  isRunning = false;
	  clearInterval(timer);
	}
  
	// Reset the timer
	function resetTimer() {
	  pauseTimer();
	  setMode(mode);
	}
  
	// Set the mode and time
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
  
	// Play the alarm sound
	function playSound() {
	  if (audio) {
		audio.play().catch(error => {
		  console.error('Audio playback failed:', error);
		});
	  }
	}
  
	// Load the alarm sound
	onMount(() => {
	  audio = new Audio('/chimes.mp3'); // Ensure this path is correct
	  audio.addEventListener('error', () => {
		console.error('Failed to load audio file.');
	  });
	});
  
	// Clean up interval on component destroy
	onDestroy(() => {
	  clearInterval(timer);
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
  
  <div class="pomodoro-timer">
	<h3>{mode} Time</h3>
	<div class="timer-display">{formatTime(time)}</div>
  
	<!-- Timer Controls -->
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
  
  <style>
	.pomodoro-timer {
	  text-align: center;
	}
  
	.timer-display {
	  font-size: 3rem;
	  margin-bottom: 1rem;
	}
  
	.timer-controls button,
	.mode-buttons button {
	  padding: 8px 12px;
	  font-size: 1rem;
	  margin: 5px;
	  cursor: pointer;
	  border: 1px solid #333;
	  background-color: #fff;
	  border-radius: 5px;
	}
  
	.timer-controls button:disabled {
	  opacity: 0.5;
	  cursor: not-allowed;
	}
  
	.timer-controls button:hover:not(:disabled),
	.mode-buttons button:hover {
	  background-color: #f0f0f0;
	}
  
	.active-mode {
	  background-color: #007bff;
	  color: white;
	}
  </style>
  