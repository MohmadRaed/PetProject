<script lang="ts">
    import { currentUser } from '$lib/stores';
    import { goto } from '$app/navigation';

    let name = '';
    let password = '';
    let error = '';

    async function handleLogin() {
        const res = await fetch('/api/auth/login', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ name, password })
        });

        if (res.ok) {
            const user = await res.json();
            currentUser.set(user);
            goto('/dashboard');
        } else {
            const errorText = await res.text();
            error = errorText || 'Login failed. Please check your credentials.';
        }
    }
</script>

<div class="container">
    <div class="box">
        <h1>🔑 Login</h1>

        <form on:submit|preventDefault={handleLogin}>
            <div>
                <label for="name">Username</label>
                <input id="name" type="text" bind:value={name} required />
            </div>
            <div>
                <label for="password">Password</label>
                <input id="password" type="password" bind:value={password} required />
            </div>
            <button type="submit">Login</button>

            {#if error}
                <p style="color: red;">{error}</p>
            {/if}

            <p>
                Don't have an account? <a href="/register">Register here</a>.
            </p>
        </form>
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
        background-color: #f4f4f9; /* Optional light background */
    }

    /* Centered content container */
    .container {
        display: flex;
        justify-content: center; /* Center content horizontally */
        align-items: center; /* Center content vertically */
        width: 100%;
    }

    /* Box styling */
    .box {
        padding: 2rem;
        border: 2px solid #0077cc; /* Blue border */
        background-color: white;
        border-radius: 8px;
        box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
        max-width: 500px;
        width: 100%;
        text-align: center;
    }

    /* Title styling with emoji */
    h1 {
        font-size: 2.5rem;
        color: #0077cc; /* Blue color */
        margin-bottom: 1rem;
    }

    /* Input field styling */
    input {
        padding: 0.5rem;
        font-size: 1rem;
        margin-top: 0.2rem;
        border-radius: 4px;
        border: 1px solid #ccc;
        width: 100%;
    }

    /* Button styling */
    button {
        padding: 0.5rem;
        font-size: 1rem;
        background-color: #0077cc;
        color: white;
        border: none;
        border-radius: 4px;
        cursor: pointer;
        width: 100%;
    }

    button:hover {
        background-color: #005fa3;
    }

    /* Error message styling */
    p {
        text-align: center;
        color: #333;
    }

    /* Link styling */
    a {
        color: #0077cc;
        text-decoration: none;
    }

    a:hover {
        text-decoration: underline;
    }
</style>
