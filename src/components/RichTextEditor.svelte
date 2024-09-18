<script>
  import { onMount } from 'svelte';
  import { writable } from 'svelte/store';

  export let content = '';

  let quill;
  let editorDiv;

  onMount(async () => {
    const Quill = (await import('quill')).default;

    quill = new Quill(editorDiv, {
      theme: 'snow',
      modules: {
        toolbar: [
          ['bold', 'italic', 'underline', 'strike'],        // toggled buttons
          ['blockquote', 'code-block'],
          [{ header: 1 }, { header: 2 }],                   // custom button values
          [{ list: 'ordered' }, { list: 'bullet' }],
          [{ script: 'sub' }, { script: 'super' }],         // superscript/subscript
          [{ indent: '-1' }, { indent: '+1' }],             // outdent/indent
          [{ direction: 'rtl' }],                           // text direction
          [{ size: ['small', false, 'large', 'huge'] }],    // custom dropdown
          [{ header: [1, 2, 3, 4, 5, 6, false] }],
          [{ color: [] }, { background: [] }],              // dropdown with defaults from theme
          [{ font: [] }],
          [{ align: [] }],
          ['clean'],                                        // remove formatting button
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
