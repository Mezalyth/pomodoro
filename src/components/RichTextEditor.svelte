<script>
  import { onMount } from 'svelte';

  export let content = '';

  let quill;
  let editorDiv;

  onMount(async () => {
    const Quill = (await import('quill')).default;

    quill = new Quill(editorDiv, {
      theme: 'snow',
      modules: {
        toolbar: [
          // Basic formatting buttons
          [{ header: [1, 2, 3, 4, 5, 6, false] }],   // Header levels
          ['bold', 'italic', 'underline', 'strike'],  // Basic formatting
          [{ indent: '-1' }, { indent: '+1' }],      // Outdent/indent
          [{ color: [] }, { background: [] }],       // Font colors and background colors
          [{ font: [] }],                            // Font family
          [{ align: [] }],                           // Text alignment
          ['clean'],                                 // Remove formatting button
        ],
      },
    });

    // Set initial content
    if (content) {
      quill.clipboard.dangerouslyPasteHTML(content);
    }

    // Update content on text change
    quill.on('text-change', () => {
      content = quill.root.innerHTML;
    });
  });
</script>

<div bind:this={editorDiv}></div>

<style global>
  /* Import Quill styles */
  @import 'quill/dist/quill.snow.css';

  /* Adjust editor styling if needed */
  .ql-editor {
    min-height: 200px;
  }
</style>
