<script lang="ts">
    import { currentUser } from '$lib/stores';
    import { goto } from '$app/navigation';

    let name = '';
    let email = '';
    let password = '';
    let error = '';

    async function handleRegister() {
        try {
            const res = await fetch('/api/auth/register', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ name, email, password })
            });

            if (res.ok) {
                const user = await res.json();
                currentUser.set(user);
                goto('/dashboard');
            } else {
                const errorText = await res.text();
                error = errorText || 'Registration failed. Try again.';
            }
        } catch (e) {
            error = 'Network error. Please check your connection.';
            console.error(e);
        }
    }
</script>

<div class="container">
    <div class="box">
        <h1>📝 Register</h1>

        <form on:submit|preventDefault={handleRegister}>
            <div>
                <label for="name">Name</label>
                <input id="name" type="text" bind:value={name} placeholder="Name" required />
            </div>
            <div>
                <label for="email">Email</label>
                <input id="email" type="email" bind:value={email} placeholder="Email" required />
            </div>
            <div>
                <label for="password">Password</label>
                <input id="password" type="password" bind:value={password} placeholder="Password" required />
            </div>
            <button type="submit">Register</button>

            {#if error}
                <p style="color: red;">{error}</p>
            {/if}

            <p>
                Already have an account? <a href="/login">Login here</a>.
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
