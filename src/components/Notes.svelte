<script>
    import { onMount } from 'svelte';

    let notes = ''; // Initialize the variable to an empty string
    let isExpanded = false; // To toggle the tray open/close
    let isHovered = false; // Detect hover to make tray peek

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
        height: 250px;
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
