<script>
	import RichTextEditor from '../components/RichTextEditor.svelte';
	import PomodoroTimer from '../components/PomodoroTimer.svelte';
	import ProgressTracker from '../components/ProgressTracker.svelte';
	import { onMount } from 'svelte';
	import { browser } from '$app/environment';
  
	let content = '';          // Rich text content from the editor
	let wordCount = 0;         // Total word count
  
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
  
	// Load saved content on component mount
	onMount(() => {
	  if (browser) {
		const savedContent = localStorage.getItem('savedContent');
		if (savedContent !== null) {
		  content = savedContent;
		}
	  }
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

	<!-- Progress Tracker Section -->
	<section class="progress-tracker-section">
		<!-- Word Goal Progress -->
		<ProgressTracker {wordCount} />
	  </section>
  
	<!-- Rich Text Editor -->
	<section class="editor-section">
	  <RichTextEditor bind:content />
	</section>
  </main>
  
  <style>
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
  
	.progress-tracker-section {
	  border-top: 1px solid #ddd;
	  padding-top: 20px;
	}
  </style>
  