<script>
  export let content = '';

  // No need for the handleInput function since bind:value handles two-way binding
</script>

<textarea
  bind:value={content}
  rows="15"
  cols="80"
  placeholder="Start writing..."
></textarea>

<style>
  textarea {
    width: 100%;
    padding: 10px;
    font-size: 1rem;
    box-sizing: border-box;
    border: 1px solid #ccc;
    border-radius: 5px;
    resize: vertical;
  }

  textarea:focus {
    outline: none;
    border-color: #007bff;
  }
</style>
