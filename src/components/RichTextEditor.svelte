<script>
  import { onMount } from 'svelte';

  export let content = ''; // Will be bound to Quill editor content

  let quill;
  let editorDiv;

  // Save content to localStorage whenever it changes
  $: if (content) {
    localStorage.setItem('quillContent', content); // Save content locally
  }

  onMount(async () => {
    const Quill = (await import('quill')).default;

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
    }

    // Update content on text change
    quill.on('text-change', () => {
      content = quill.root.innerHTML; // Update the content variable
    });
  });
</script>

<div bind:this={editorDiv}></div>

<style global>
  @import 'quill/dist/quill.snow.css';
  .ql-editor {
    min-height: 200px;
  }
</style>
