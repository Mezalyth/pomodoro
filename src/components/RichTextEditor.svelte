<script>
  import { onMount } from 'svelte';
  import 'quill/dist/quill.snow.css'; // Ensure Quill's CSS is loaded

  let editor;
  export let content = '';  // Export content to be bound to parent

  function updateContent() {
    content = editor.root.innerHTML; // Quill's output as HTML
    console.log('Updated Content:', content);  // Log the updated content
  }

  onMount(async () => {
    if (typeof window !== 'undefined' && !editor) { // Check if Quill has already been initialized
      const Quill = (await import('quill')).default;

      // Initialize Quill editor only once
      editor = new Quill('#editor', {
        theme: 'snow',  // Apply the Snow theme
        modules: {
          toolbar: [
            [{ 'header': [1, 2, false] }],
            ['bold', 'italic', 'underline'],
            ['link', 'blockquote', 'code-block'],
            [{ 'list': 'ordered' }, { 'list': 'bullet' }],
            ['clean'] // Removes formatting
          ]
        }
      });

      editor.on('text-change', updateContent);  // Update content on text-change
    }
  });
</script>

<style>
  #editor {
    height: 300px;
    background: white;
    border: 1px solid #ccc;
  }
</style>

<!-- Quill Editor HTML Element -->
<div id="editor"></div>
