<script>
    import { onMount } from 'svelte';  // Import the onMount lifecycle function
  
    let time = 1500; // Default to 25 minutes (1500 seconds)
    let timer;
    let isRunning = false;
    let mode = "Work"; // Default to Work mode
    let audio; // Audio reference
  
    // Durations in seconds
    const WORK_DURATION = 1500; // 25 minutes
    const SHORT_BREAK_DURATION = 300; // 5 minutes
    const LONG_BREAK_DURATION = 900; // 15 minutes
  
    // Start the timer
    function startTimer() {
      if (!isRunning) {
        isRunning = true;
        timer = setInterval(() => {
          if (time > 0) {
            time -= 1;
          } else {
            playSound(); // Play sound when time reaches 0
            stopTimer(); // Stop the timer when time is up
          }
        }, 1000);
      }
    }
  
    // Pause the timer
    function pauseTimer() {
      isRunning = false;
      clearInterval(timer);
    }
  
    // Stop the timer completely when time is up
    function stopTimer() {
      isRunning = false;
      clearInterval(timer);
    }
  
    // Reset the timer (and stop it)
    function resetTimer() {
      pauseTimer();
      setMode(mode); // Reset to the current mode's time
    }
  
    // Manually set the mode (Work, Short Break, or Long Break)
    function setMode(newMode) {
      mode = newMode;
      if (mode === "Work") {
        time = WORK_DURATION;
      } else if (mode === "Short Break") {
        time = SHORT_BREAK_DURATION;
      } else if (mode === "Long Break") {
        time = LONG_BREAK_DURATION;
      }
    }
  
    // Format time as mm:ss
    function formatTime(seconds) {
      const minutes = Math.floor(seconds / 60);
      const secs = seconds % 60;
      return `${minutes}:${secs < 10 ? "0" : ""}${secs}`;
    }
  
    // Play the chimes sound
    function playSound() {
      if (audio) {
        audio.play();
      }
    }
  
    // Initialize the audio when the component mounts
    onMount(() => {
      audio = new Audio('/chimes.mp3'); // Load the chimes audio from the static folder
    });
  </script>
  
  <div>
    <h3>{mode} Time</h3>
    <p>{formatTime(time)}</p>
  
    <!-- Timer Controls -->
    <button on:click={startTimer} disabled={isRunning}>Start</button>
    <button on:click={pauseTimer} disabled={!isRunning || time === 0}>Pause</button>
    <button on:click={resetTimer}>Reset</button>
  
    <!-- Manual Mode Setters -->
    <div>
      <button on:click={() => setMode("Work")}>Pomodoro (Work)</button>
      <button on:click={() => setMode("Short Break")}>Short Break</button>
      <button on:click={() => setMode("Long Break")}>Long Break</button>
    </div>
  </div>
  