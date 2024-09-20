<script>
    import { onMount } from 'svelte';
  
    let notes = ''; // Initialize the variable to an empty string
    let isExpanded = false; // To toggle the tray open/close
    let isHovered = false; // Detect hover to make tray peek
  
    // To-do list variables
    let todos = []; // Array to store to-do items
    let newTodo = ''; // Model for the new to-do input
  
    // Event to detect click outside and close the tray
    onMount(() => {
      document.addEventListener('click', handleClickOutside);
  
      // Clean up event listener on component destroy
      return () => {
        document.removeEventListener('click', handleClickOutside);
      };
    });
  
    // Function to handle clicks outside the tray
    function handleClickOutside(event) {
      const tray = document.querySelector('.notes-container');
      if (tray && !tray.contains(event.target) && isExpanded) {
        isExpanded = false;
      }
    }
  
    // Peek the tray when the mouse is near the left edge
    function handleMouseMove(event) {
      const threshold = 50; // Distance from the edge for peek
      if (event.clientX < threshold && !isExpanded) {
        isHovered = true;
      } else {
        isHovered = false;
      }
    }
  
    onMount(() => {
      document.addEventListener('mousemove', handleMouseMove);
      return () => {
        document.removeEventListener('mousemove', handleMouseMove);
      };
    });
  
    // Function to add a new to-do item
    function addTodo() {
      if (newTodo.trim() !== '') {
        todos = [...todos, { text: newTodo, id: Date.now(), completed: false }];
        newTodo = ''; // Clear the input
      }
    }
  
    // Function to toggle the completion status of a to-do item
    function toggleTodo(id) {
      todos = todos.map(todo => {
        if (todo.id === id) {
          return { ...todo, completed: !todo.completed };
        }
        return todo;
      });
    }
  
    // Function to clear completed tasks
    function clearCompleted() {
      todos = todos.filter(todo => !todo.completed);
    }
  </script>
  
  <!-- Slide-out Notes tray -->
  <div 
    class="notes-container {isExpanded ? 'expanded' : ''} {isHovered && !isExpanded ? 'peek' : ''}" 
    role="complementary" 
    aria-hidden={!isExpanded} 
    on:click={(event) => event.stopPropagation()}
  >
    <div class="notes-content">
      <h3>Notes</h3>
      <!-- Binding to the 'notes' variable -->
      <textarea bind:value={notes} placeholder="Ideas, goals, inspiration..."></textarea>
  
      <!-- To-do List Section -->
      <div class="todo-section">
        <h3>To-Do List</h3>
        <!-- Input for new to-do items -->
        <div class="add-todo">
          <input
            type="text"
            placeholder="Add a new task..."
            bind:value={newTodo}
            on:keydown={(e) => { if (e.key === 'Enter') addTodo(); }}
          />
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
    on:click={(event) => { event.stopPropagation(); isExpanded = !isExpanded; }}
    aria-expanded={isExpanded}
    aria-label={isExpanded ? "Close Notes" : "Open Notes"}
  >
    {isExpanded ? 'Close Notes' : 'Open Notes'}
  </button>
  
  <style>
    .notes-container {
      position: fixed;
      top: 0;
      left: -320px; /* Hidden by default */
      width: 320px;
      height: 100%;
      background-color: #fff;
      border-right: 2px solid #ddd;
      box-shadow: 2px 0 10px rgba(0, 0, 0, 0.1);
      transition: left 0.3s ease;
      z-index: 999;
      padding: 20px;
      overflow-y: auto; /* Add scroll if content overflows */
    }
  
    .notes-container.expanded {
      left: 0; /* Slide it in when expanded */
    }
  
    .notes-container.peek {
      left: -290px; /* Partially peek out */
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
      background-color: #28a745;
      color: white;
      cursor: pointer;
      border-radius: 0 5px 5px 0;
    }
  
    .add-todo button:hover {
      background-color: #218838;
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
      background-color: #dc3545;
      color: white;
      cursor: pointer;
      border-radius: 5px;
    }
  
    .clear-completed:hover {
      background-color: #c82333;
    }
  
    /* The button that sticks to the side of the tray */
    .toggle-button {
      position: fixed;
      top: 30%;
      left: 40px; /* Make it visible even when the tray is hidden */
      background-color: #007bff;
      color: white;
      border: none;
      padding: 16px 50px;
      cursor: pointer;
      border-radius: 5px;
      z-index: 1000;
      transform: rotate(90deg); /* Rotated 90 degrees for vertical text */
      transform-origin: left center;
      transition: left 0.3s ease, transform 0.3s ease;
    }
  
    /* Button moves with the tray */
    .expanded-button {
      left: 360px; /* Moves out with the tray when expanded */
    }
  
    .toggle-button:hover {
      background-color: #0056b3;
    }
  </style>
  