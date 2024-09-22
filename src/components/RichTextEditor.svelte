<!-- RichTextEditor.svelte -->
<script>
  import { onMount } from 'svelte';
  import { browser } from '$app/environment';

  export let content = ''; // Bound to Quill editor content
  let quill;
  let editorDiv;

  onMount(async () => {
    if (browser) {
      try {
        // Dynamically import Quill to ensure it only runs in the browser
        const Quill = (await import('quill')).default;

        // Initialize Quill editor
        quill = new Quill(editorDiv, {
          theme: 'snow',
          modules: {
            toolbar: [
              [{ header: [1, 2, 3, 4, 5, 6, false] }],
              ['bold', 'italic', 'underline', 'strike'],
              [{ indent: '-1' }, { indent: '+1' }],
              [{ color: [] }, { background: [] }],
              [{ font: [] }],
              [{ align: [] }],
              ['clean'],
            ],
          },
        });

        // Load content from localStorage when the page loads
        const savedContent = localStorage.getItem('quillContent');
        if (savedContent) {
          quill.clipboard.dangerouslyPasteHTML(savedContent);
          content = savedContent; // Initialize bound content
        }

        // Update content on text change and save to localStorage
        quill.on('text-change', () => {
          content = quill.root.innerHTML; // Update the content variable
          localStorage.setItem('quillContent', content); // Save to localStorage
        });
      } catch (error) {
        console.error('Error loading Quill:', error);
      }
    }
  });
</script>

<!-- Editor Container -->
<div bind:this={editorDiv}></div>

<style global>
  /* Import Quill's Snow theme CSS */
  @import 'quill/dist/quill.snow.css';

  /* Set a minimum height for the editor */
  .ql-editor {
    min-height: 200px;
  }

</style>
