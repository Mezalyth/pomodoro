<!-- +page.svelte -->
<script>
	import RichTextEditor from '../components/RichTextEditor.svelte';
	import PomodoroTimer from '../components/PomodoroTimer.svelte';
	import ProgressTracker from '../components/ProgressTracker.svelte';
	import Notes from '../components/Notes.svelte';
	import { browser } from '$app/environment'; 
	import '../app.css';
  
	let content = '';  // Bound to the rich text editor's content
	let wordCount = 0;  // Total word count
  
	// Function to get the word count from HTML content
	function getWordCount(htmlContent) {
	  if (!htmlContent || !browser) return 0;  
  
	  const tempDiv = document.createElement('div'); 
	  tempDiv.innerHTML = htmlContent;
	  const textContent = tempDiv.textContent || tempDiv.innerText || '';
  
	  return textContent.trim().split(/\s+/).filter(word => word.length > 0).length;
	}
  
	// Reactive word count update
	$: if (browser) {  
	  wordCount = getWordCount(content);
	}
  
	async function copyToClipboard() {
	  if (browser && content) {
		try {
		  const clipboardItem = new ClipboardItem({
			'text/html': new Blob([content], { type: 'text/html' }),
		  });
  
		  await navigator.clipboard.write([clipboardItem]);
		  alert('Formatted content copied to clipboard!');
		} catch (err) {
		  console.error('Failed to copy formatted content: ', err);
		  alert('Failed to copy content. Please try again.');
		}
	  }
	}
  </script>
  
  <main>
	<h1>WriteAway</h1>
  
	<!-- Notes Tray on the Left -->
	<section class="notes-section">
	  <Notes />
	</section>
  
	<!-- Pomodoro Timer -->
	<section class="pomodoro-section">
	  <PomodoroTimer />
	</section>
  
	<!-- Progress Tracker Section -->
	<section class="progress-tracker-section">
	  <ProgressTracker {wordCount} />
	</section>
  
	<!-- Rich Text Editor -->
	<section class="editor-section">
	  <RichTextEditor bind:content />
	</section>
  
	<!-- Copy to Clipboard Button -->
	<section class="clipboard-section">
	  <button on:click={copyToClipboard}>Copy to Clipboard</button>
	</section>
  </main>
  
  <style>
	main {
	  font-family: sans-serif;
	  max-width: 800px;
	  margin: 0 auto;
	}
  
	h1 {
	  font-size: 0.8rem;
	  text-align: center;
	}
  
	.pomodoro-section,
	.notes-section {
	  text-align: center;
	}
  
	.progress-tracker-section {
	  border-top: 1px solid #ddd;
	}
  
	.clipboard-section {
	  text-align: center;
	  margin-top: 20px;
	}
  
	button {
	  padding: 10px 20px;
	  background-color: #4CAF50;
	  color: white;
	  border: none;
	  border-radius: 5px;
	  cursor: pointer;
	  font-size: 1rem;
	}
  
	button:hover {
	  background-color: #45a049;
	}
  </style>
  