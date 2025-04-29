<script lang="ts">
    import { currentUser } from '$lib/stores';
    import { goto } from '$app/navigation';

    $: user = $currentUser;

    function logout() {
        currentUser.set(null);
        goto('/login');
    }
</script>

<nav>
    <a href="/">Home</a>
    {#if user}
        <a href="/dashboard">Dashboard</a>
        <a href="/logs">Logs</a>
        <a href="/shop">Shop</a>
        <a href="/pets">Adopt</a>
        {#if user.isAdmin}
            <a href="/admin">Admin Panel</a>
        {/if}
        <button on:click={logout}>Logout</button>
        <span class="user">{user.name}</span>
    {:else}
        <a href="/login">Login</a>
        <a href="/register">Register</a>
    {/if}
</nav>

<slot />

<style>
    /* Centering the entire layout */
    body {
        display: flex;
        justify-content: center; /* Center horizontally */
        align-items: center; /* Center vertically */
        height: 100vh; /* Full viewport height */
        margin: 0; /* Remove default margin */
        background-color: #f4f4f9; /* Optional light background color */
    }

    /* Centering content within the nav */
    nav {
        background: #f2f2f2;
        padding: 1rem;
        display: flex;
        gap: 1rem;
        align-items: center;
        justify-content: center; /* Center the nav content horizontally */
        width: 100%;
        max-width: 1200px; /* Optional: limits max width of nav */
        border-bottom: 1px solid #ccc;
    }

    nav a {
        text-decoration: none;
        color: #333;
         font-size: 0.9rem;
    }

    nav .user {
        margin-left: auto;
        font-weight: bold;
    }

    nav button {
        background: transparent;
        border: none;
        cursor: pointer;
        color: #0077cc;
         font-size: 0.9rem;
    }

    nav button:hover {
        color: #005fa3;
    }
</style>
