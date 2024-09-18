<script>
	import RichTextEditor from '../components/RichTextEditor.svelte';
	import PomodoroTimer from '../components/PomodoroTimer.svelte';
	import ProgressTracker from '../components/ProgressTracker.svelte';
	import { onMount } from 'svelte';
	import { browser } from '$app/environment';
  
	let content = '';           // Rich text content from the editor
	let wordCount = 0;          // Total word count
	let wordsThisSession = 0;   // Words written in this session
	let initialWordCount = 0;   // Initial word count at session start
  
	// Function to get the word count from HTML content
	function getWordCount(htmlContent) {
	  if (!htmlContent) return 0;
  
	  // Create a temporary element to parse HTML content
	  const tempDiv = document.createElement('div');
	  tempDiv.innerHTML = htmlContent;
	  const textContent = tempDiv.textContent || tempDiv.innerText || '';
  
	  // Split the text content into words
	  return textContent.trim().split(/\s+/).filter(word => word.length > 0).length;
	}
  
	// Reactive statement to update total word count
	$: wordCount = getWordCount(content);
  
	// Reactive statement to track words written in this session
	$: wordsThisSession = Math.max(0, wordCount - initialWordCount);
  
	// Reset the session word count manually
	function resetSessionWordCount() {
	  if (confirm('Are you sure you want to reset the session word count?')) {
		initialWordCount = wordCount;
		wordsThisSession = 0;
	  }
	}
  
	// Load saved content on component mount
	onMount(() => {
	  if (browser) {
		const savedContent = localStorage.getItem('savedContent');
		if (savedContent !== null) {
		  content = savedContent;
		}
	  }
	  initialWordCount = wordCount; // Initialize the initial word count
	});
  
	// Save content to localStorage whenever it changes
	$: if (browser) {
	  localStorage.setItem('savedContent', content);
	}
  </script>
  
  <main>
	<h1>WriteAway - Rich Text Editor</h1>
  
	<!-- Pomodoro Timer -->
	<section class="pomodoro-section">
	  <PomodoroTimer />
	</section>
  
	<!-- Rich Text Editor -->
	<section class="editor-section">
	  <RichTextEditor bind:content />
	</section>
  
	<!-- Word Tracking Section -->
	<section class="word-tracking-section">
	  <h3>Word Count</h3>
	  <p>Total Words: {wordCount}</p>
  
	  <!-- Words Written This Session -->
	  <h3>Words Written This Session</h3>
	  <p>Words this session: {wordsThisSession}</p>
	  <button on:click={resetSessionWordCount}>Reset Session Word Count</button>
  
	  <!-- Word Goal Progress -->
	  <ProgressTracker {wordCount} />
	</section>
  </main>
  
  <style>
	/* Your existing styles */
	main {
	  padding: 20px;
	  font-family: sans-serif;
	  max-width: 800px;
	  margin: 0 auto;
	}
  
	h1 {
	  font-size: 2.5rem;
	  margin-bottom: 20px;
	  text-align: center;
	}
  
	section {
	  margin-bottom: 30px;
	}
  
	.editor-section {
	  margin-top: 20px;
	}
  
	.pomodoro-section {
	  text-align: center;
	}
  
	.word-tracking-section {
	  border-top: 1px solid #ddd;
	  padding-top: 20px;
	}
  
	button {
	  padding: 8px 12px;
	  font-size: 1rem;
	  cursor: pointer;
	  border: 1px solid #333;
	  background-color: #fff;
	  border-radius: 5px;
	  margin-top: 10px;
	}
  
	button:hover {
	  background-color: #f0f0f0;
	}
  </style>
  