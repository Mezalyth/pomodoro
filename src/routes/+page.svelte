<script>
	import RichTextEditor from '../components/RichTextEditor.svelte';
	import PomodoroTimer from '../components/PomodoroTimer.svelte';
	import WordCounter from '../components/WordCounter.svelte';
	import ProgressTracker from '../components/ProgressTracker.svelte';
  
	let content = '';  // Rich text content from the editor
	let wordCount = 0; // Track word count separately
  
	// Function to strip HTML tags and count plain words
	function getWordCount(htmlContent) {
	  if (!htmlContent) return 0;
  
	  // Strip HTML tags
	  const plainText = htmlContent.replace(/<[^>]+>/g, '').trim();
	  
	  // Split by whitespace and filter out empty strings
	  return plainText.split(/\s+/).filter(word => word.length > 0).length;
	}
  
	// Reactive statement to update word count when content changes
	$: wordCount = getWordCount(content);
  </script>
  
  <main>
	<h1>WriteAway - Rich Text Editor</h1>
  
	<!-- Pomodoro Timer -->
	<PomodoroTimer />
  
	<!-- Rich Text Editor -->
	<RichTextEditor bind:content />
  
	<!-- Word Counter -->
	<h3>Word Count</h3>
	<WordCounter content={content} />
  
	<!-- Word Goal Progress -->
	<ProgressTracker wordCount={wordCount} />
  
  </main>
  
  <style>
	main {
	  padding: 20px;
	  font-family: sans-serif;
	}
  
	h1 {
	  font-size: 2rem;
	  margin-bottom: 20px;
	}
  
	h3 {
	  margin-top: 20px;
	}
  
	div {
	  white-space: pre-wrap;
	  background-color: #f9f9f9;
	  padding: 10px;
	  border: 1px solid #ddd;
	  border-radius: 5px;
	}
  </style>
  