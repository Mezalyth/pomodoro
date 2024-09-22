<!-- Notes.svelte -->
<script>
    import { onMount } from 'svelte';
  
    // State Variables
    let notes = ''; // User's notes (scratch pad)
    let todos = []; // Array of to-do items
    let newTodo = ''; // Input for new to-do
  
    let isExpanded = false; // Toggle tray open/close
    let isHovered = false; // Hover state for peek functionality
  
    // Constants
    const PEAK_THRESHOLD = 200; // Distance from the edge to trigger peek (in pixels)
    const STORAGE_KEY_NOTES = 'svelte-notes-app'; // Key for localStorage
  
    /**
     * Saves the current state (notes, todos, isExpanded) to localStorage.
     * Utilizes try-catch for robust error handling.
     */
    function saveState() {
      try {
        const state = {
          notes,
          todos,
          isExpanded
        };
        localStorage.setItem(STORAGE_KEY_NOTES, JSON.stringify(state));
        console.log('State saved:', state); // Debugging log
      } catch (error) {
        console.error('Failed to save Notes state to localStorage:', error);
      }
    }
  
    /**
     * Loads the state (notes, todos, isExpanded) from localStorage.
     * Utilizes try-catch for robust error handling.
     */
    function loadState() {
      try {
        const savedState = localStorage.getItem(STORAGE_KEY_NOTES);
        if (savedState) {
          const { notes: savedNotes, todos: savedTodos, isExpanded: savedIsExpanded } = JSON.parse(savedState);
          notes = savedNotes || '';
          todos = savedTodos || [];
          isExpanded = savedIsExpanded || false;
          console.log('State loaded:', { notes, todos, isExpanded }); // Debugging log
        } else {
          console.log('No saved state found.');
        }
      } catch (error) {
        console.error('Failed to load Notes state from localStorage:', error);
      }
    }
  
    /**
     * Handles clicks outside the notes tray to close it.
     * @param {Event} event - The click event.
     */
    function handleClickOutside(event) {
      const tray = document.querySelector('.notes-container');
      if (tray && !tray.contains(event.target) && isExpanded) {
        isExpanded = false;
        saveState();
        console.log('Tray closed by clicking outside.');
      }
    }
  
    /**
     * Handles mouse movement to trigger peek behavior.
     * Peeks out the tray when the mouse is within PEAK_THRESHOLD pixels from the left edge.
     * @param {MouseEvent} event - The mousemove event.
     */
    function handleMouseMove(event) {
      if (event.clientX < PEAK_THRESHOLD && !isExpanded) {
        isHovered = true;
        saveState();
        console.log('Tray is peeking.');
      } else {
        isHovered = false;
        saveState();
        console.log('Tray is hidden.');
      }
    }
  
    /**
     * Adds a new to-do item to the list.
     * Clears the input field after adding.
     */
    function addTodo() {
      if (newTodo.trim() !== '') {
        todos = [...todos, { text: newTodo, id: Date.now(), completed: false }];
        console.log('Added new todo:', newTodo);
        newTodo = ''; // Clear the input
        saveState();
      }
    }
  
    /**
     * Toggles the completion status of a to-do item.
     * @param {number} id - The ID of the to-do item to toggle.
     */
    function toggleTodo(id) {
      todos = todos.map(todo => {
        if (todo.id === id) {
          console.log('Toggled todo with id:', id);
          return { ...todo, completed: !todo.completed };
        }
        return todo;
      });
      saveState();
    }
  
    /**
     * Clears all completed to-do items from the list.
     */
    function clearCompleted() {
      todos = todos.filter(todo => !todo.completed);
      console.log('Cleared all completed todos.');
      saveState();
    }
  
    /**
     * Initializes the component by loading the saved state and setting up event listeners.
     * Cleans up event listeners when the component is destroyed.
     */
    onMount(() => {
      loadState();
  
      // Add event listeners for clicks and mouse movements
      document.addEventListener('click', handleClickOutside);
      document.addEventListener('mousemove', handleMouseMove);
  
      // Cleanup event listeners on component destroy
      return () => {
        document.removeEventListener('click', handleClickOutside);
        document.removeEventListener('mousemove', handleMouseMove);
      };
    });
  
    /**
     * Reactive statement to save state whenever 'notes', 'todos', or 'isExpanded' changes.
     * This ensures that any change is immediately persisted to localStorage.
     */
    // Removed the reactive saveState to prevent unintended overwrites during load
</script>

<!-- Slide-out Notes Tray -->
<div 
  class="notes-container {isExpanded ? 'expanded' : ''} {isHovered && !isExpanded ? 'peek' : ''}" 
  role="complementary" 
  aria-hidden={!isExpanded} 
  on:click={(event) => event.stopPropagation()} 
>
  <div class="notes-content">
    <h3 class="header">Notes</h3>
    <!-- Binding to the 'notes' variable -->
    <textarea bind:value={notes} placeholder="Ideas, goals, inspiration..."></textarea>

    <!-- To-do List Section -->
    <div class="todo-section">
      <h3 class="header">Tasks</h3>
      <!-- Input for new to-do items -->
      <div class="add-todo">
        <input
          type="text"
          placeholder="Add a new task..."
          bind:value={newTodo}
          on:keydown={(e) => { if (e.key === 'Enter') addTodo(); }}
        /> <!-- Properly self-closed the input tag -->
        <button on:click={addTodo} aria-label="Add To-Do">Add</button>
      </div>
      <!-- Display the list of to-do items -->
      <ul class="todo-list">
        {#each todos as todo (todo.id)}
          <li class:completed={todo.completed}>
            <label>
              <input
                type="checkbox"
                checked={todo.completed}
                on:change={() => toggleTodo(todo.id)}
              />
              <span>{todo.text}</span>
            </label>
          </li>
        {/each}
      </ul>
      <!-- Show Clear Completed button if there are completed tasks -->
      {#if todos.some(todo => todo.completed)}
        <button class="clear-completed" on:click={clearCompleted}>
          Clear Completed
        </button>
      {/if}
    </div>
  </div>
</div>

<!-- Toggle button stuck to the side of the tray (vertically oriented) -->
<button 
  class="toggle-button {isExpanded ? 'expanded-button' : ''}"
  on:click={(event) => { 
    event.stopPropagation(); 
    isExpanded = !isExpanded; 
    saveState(); 
    console.log('Toggle button clicked. isExpanded:', isExpanded);
  }}
  aria-expanded={isExpanded}
  aria-label={isExpanded ? "Close Notes" : "Open Notes"}
>
  {isExpanded ? 'Close Notes' : 'Open Notes'}
</button>

<style>
  /* Common header styling for both headers */
  .header {
    text-align: center;
    margin-bottom: 10px;
    font-size: 1.5rem;
    color: #333;
  }

  /* Notes Tray Styles */
  .notes-container {
    position: fixed;
    top: 0;
    left: -320px; /* Hidden by default */
    width: 280px;
    height: 100%;
    background: #f1f1f1;
    box-shadow: 2px 0 10px rgba(0, 0, 0, 0.1);
    transition: left 0.3s ease;
    z-index: 999; /* Ensure it appears above other elements */
    padding: 20px;
    overflow-y: auto; /* Add scroll if content overflows */
  }

  .notes-container.expanded {
    left: 0; /* Slide it in when expanded */
  }

  .notes-container.peek {
    left: -270px; /* Partially peek out */
  }

  .notes-content {
    text-align: center;
    margin: 20px auto; /* Add margin around the content */
    max-width: 100%; /* Ensure content stays within the width */
    box-sizing: border-box; /* Ensure padding/margins are included in element width */
  }

  textarea {
    width: 100%;
    height: 150px;
    padding: 15px; /* Add padding for better text spacing */
    font-size: 1rem;
    border: 1px solid #ccc;
    border-radius: 5px;
    resize: vertical;
    margin-top: 10px; /* Add margin for breathing room */
    box-sizing: border-box; /* Ensure padding is included in width */
  }

  textarea::placeholder {
    color: #888; /* Style the placeholder text */
    font-style: italic;
  }

  /* To-do List Styles */
  .todo-section {
    margin-top: 30px;
    text-align: left;
  }

  .add-todo {
    display: flex;
    margin-bottom: 20px;
  }

  .add-todo input {
    flex: 1;
    padding: 10px;
    font-size: 1rem;
    border: 1px solid #ccc;
    border-radius: 5px 0 0 5px;
    box-sizing: border-box;
  }

  .add-todo button {
    padding: 10px 20px;
    font-size: 1rem;
    border: none;
    background-color: #1e6a52;
    color: white;
    cursor: pointer;
    border-radius: 0 5px 5px 0;
    transition: background-color 0.2s ease;
  }

  .add-todo button:hover {
    background-color: #227c5f;
  }

  .todo-list {
    list-style: none;
    padding: 0;
    margin: 0;
  }

  .todo-list li {
    display: flex;
    align-items: center;
    margin-bottom: 10px;
  }

  .todo-list li.completed {
    opacity: 0.6;
  }

  .todo-list label {
    display: flex;
    align-items: center;
    cursor: pointer;
    width: 100%;
  }

  .todo-list input[type="checkbox"] {
    margin-right: 10px;
    transform: scale(1.2);
  }

  .todo-list span {
    font-size: 1rem;
  }

  .todo-list li.completed span {
    text-decoration: line-through;
    color: #999;
  }

  /* Style for Clear Completed button */
  .clear-completed {
    margin-top: 10px;
    padding: 10px 20px;
    font-size: 1rem;
    border: none;
    background-color: #7c2522;
    color: white;
    cursor: pointer;
    border-radius: 5px;
    transition: background-color 0.2s ease;
  }

  .clear-completed:hover {
    background-color: #942c29;
  }

  /* Toggle Button Styles */
  .toggle-button {
    position: fixed;
    top: 30%;
    left: 40px; /* Make it visible even when the tray is hidden */
    background-color: #37699e;
    color: white;
    border: none;
    padding: 16px 50px;
    cursor: pointer;
    border-radius: 5px;
    z-index: 1000; /* Ensure it appears above the tray */
    transform: rotate(90deg); /* Rotated 90 degrees for vertical text */
    transform-origin: left center;
    transition: left 0.3s ease, transform 0.3s ease;
  }

  /* Button moves with the tray */
  .expanded-button {
    left: 340px; /* Moves out with the tray when expanded */
  }

  .toggle-button:hover {
    background-color: #0056b3;
  }

  /* Responsive Design */
  @media (max-width: 600px) {
    .notes-container {
      width: 80%;
      left: -80%;
      background: #f1f1f1;
    }

    .notes-container.expanded {
      left: 0;
    }

    .notes-container.peek {
      left: -70%;
    }

    .toggle-button {
      left: 20px;
      padding: 12px 30px;
      font-size: 0.9rem;
    }

    .toggle-button.expanded-button {
      left: 80%;
    }
  }
</style>
