<script lang="ts">
    import { onMount } from 'svelte';
    let logs: string[] = [];
    let error = '';

    onMount(async () => {
        const res = await fetch('/api/log');
        if (res.ok) {
            logs = await res.json();
        } else {
            error = 'Failed to load logs';
        }
    });
</script>


<div class="container">
    <div class="box">
        <h1>💻 Action Log</h1>

        {#if error}
            <p style="color: red;">{error}</p>
        {:else if logs.length === 0}
            <p>No actions have been logged yet.</p>
        {:else}
            <ul>
                {#each logs as log}
                    <li>{log}</li>
                {/each}
            </ul>
        {/if}
    </div>
</div>

<style>
    /* Center the body content */
    body {
        display: flex;
        justify-content: center; /* Center horizontally */
        align-items: center; /* Center vertically */
        height: 100vh; /* Full viewport height */
        margin: 0; /* Removes default margin */
        background-color: black; /* Black background */
        color: #00ff00; /* Green text color */
        font-family: 'Courier New', Courier, monospace; /* Monospace font for hacker feel */
    }

    /* Container to center the content */
    .container {
        display: flex;
        justify-content: center;
        align-items: center;
        width: 100%;
    }

    /* Box styling */
    .box {
        padding: 2rem;
        border: 2px solid #00ff00; /* Green border */
        background-color: #1a1a1a; /* Dark background inside the box */
        border-radius: 8px;
        box-shadow: 0 4px 10px rgba(0, 0, 0, 0.3);
        max-width: 800px;
        width: 100%;
        text-align: center;
    }

    /* Title styling with emoji */
    h1 {
        font-size: 2.5rem;
        color: #00ff00; /* Green text for title */
        margin-bottom: 1rem;
    }

    /* List styling */
    ul {
        list-style: none;
        padding: 0;
    }

    li {
        font-size: 1.2rem;
        color: #00ff00; /* Ensuring green text for the logs */
        margin: 0.5rem 0;
    }

    /* Error message styling */
    p {
        font-size: 1rem;
        color: red;
    }

    /* Link styling */
    a {
        color: #00ff00;
        text-decoration: none;
    }

    a:hover {
        text-decoration: underline;
    }
</style>
