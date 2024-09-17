<script>
	import TextEditor from '../components/TextEditor.svelte';
	import WordCounter from '../components/WordCounter.svelte';
	import ProgressTracker from '../components/ProgressTracker.svelte';
	import PomodoroTimer from '../components/PomodoroTimer.svelte';
  
	let content = '';
  
	function updateWordCount(newContent) {
	  content = newContent;
	}
  </script>
  
  <main>
	<h1>WriteAway - Finish Your Story</h1>
  
	<!-- Pomodoro Timer -->
	<PomodoroTimer />
  
	<!-- Text Editor -->
	<TextEditor {content} {updateWordCount} />
  
	<!-- Word Counter -->
	<WordCounter {content} />
  
	<!-- Goal Progress Tracker -->
	<ProgressTracker wordCount={content.split(/\s+/).filter(word => word.length > 0).length} />
  </main>
  