<script>
    export let content = '';
  
    // Calculate the word count based on the current content
    $: wordCount = content.split(/\s+/).filter(word => word.length > 0).length;
  </script>
  
  <div>
    <p>Total Words: {wordCount}</p>
  </div>
  