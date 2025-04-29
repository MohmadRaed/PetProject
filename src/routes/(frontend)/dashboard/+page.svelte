<script lang="ts">
    import { onMount } from 'svelte';
    import { currentUser } from '$lib/stores';
    import type { Pet, SafeUser } from '$lib/types';
    import { goto } from '$app/navigation';

    let pets: Pet[] = [];
    let error = '';
    let success = '';

    $: user = $currentUser;


    async function loadPets() {
        if (!user) {
            goto('/login');
            return;
        }

        const res = await fetch('/api/pets');
        if (res.ok) {
            const allPets = await res.json();
            // Filter for pets owned by the current user
            pets = allPets.filter((pet: Pet) => pet.owner === user.id);
        } else {
            error = 'Failed to load pets';
        }
    }

    async function handleAction(petId: number, action: 'feed' | 'toy' | 'return') {
        if (!user) {
            error = 'You need to be logged in to perform this action';
            goto('/login');
            return;
        }

        const res = await fetch('/api/actions', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                userId: user.id,
                petId: petId,
                action: action
            })
        });

        if (res.ok) {
            success = `Pet successfully ${action === 'return' ? 'returned' : action + 'ed'}`;
            loadPets(); // Reload pets after action
        } else {
            const errorText = await res.text();
            error = `Failed to ${action} pet: ${errorText}`;
        }
    }

    onMount(() => {
        if (!user) {
            goto('/login');
        } else {
            loadPets();
        }
    });
</script>

<div class="container">
    <div class="box">
        <h1>📋 Your Adopted Pets</h1>

        {#if success}
            <p style="color: green;">{success}</p>
        {/if}
        {#if error}
            <p style="color: red;">{error}</p>
        {/if}

        {#if pets.length === 0}
            <p>You haven't adopted any pets yet.</p>
        {:else}
            <ul>
                {#each pets as pet}
                    <li>
                        <h3>{pet.name} ({pet.type})</h3>
                        <div>Happiness: {pet.happiness}/100</div>
                        <div>Hunger: {pet.hunger}/100</div>
                        <div class="actions">
                            <button on:click={() => handleAction(pet.id, 'feed')}>Feed</button>
                            <button on:click={() => handleAction(pet.id, 'toy')}>Play</button>
                            <button on:click={() => handleAction(pet.id, 'return')}>Return</button>
                        </div>
                    </li>
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
        max-width: 800px;
        width: 100%;
        text-align: center;
    }

    /* Title styling with emoji */
    h1 {
        font-size: 2.5rem;
        color: #0077cc; /* Blue color */
        margin-bottom: 1rem;
    }

    /* List styling */
    ul {
        list-style: none;
        padding: 0;
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
        gap: 1rem;
    }

    li {
        border: 1px solid #ccc;
        padding: 1rem;
        border-radius: 5px;
        background-color: #f9f9f9;
    }

    /* Actions styling */
    .actions {
        display: flex;
        gap: 0.5rem;
        margin-top: 1rem;
    }

    button {
        padding: 0.5rem;
        font-size: 1rem;
        background-color: #0077cc;
        color: white;
        border: none;
        border-radius: 4px;
        cursor: pointer;
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
