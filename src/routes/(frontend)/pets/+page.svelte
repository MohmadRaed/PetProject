<script lang="ts">
    import { onMount } from 'svelte';
    import { currentUser } from '$lib/stores';
    import type { Pet, SafeUser } from '$lib/types';
    import { goto } from '$app/navigation';

    let pets: Pet[] = [];
    let filteredPets: Pet[] = [];
    let selectedType = '';
    let error = '';
    let success = '';

    $: user = $currentUser;
    $: filteredPets = selectedType
        ? pets.filter(pet => pet.type === selectedType)
        : pets;

    onMount(() => {
        console.log('Page loaded');
        loadPets();
    });


    async function loadPets() {
        const res = await fetch('/api/pets');
        if (res.ok) {
            pets = await res.json();
            console.log('Loaded pets:', pets); //
        } else {
            error = 'Failed to load pets';
            console.error('Failed to load pets');
        }
    }


    async function adoptPet(petId: number) {
        if (!user) {
            goto('/login');
            return;
        }

        const res = await fetch('/api/adopt', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ userId: user.id, petId })
        });

        if (res.ok) {
            success = 'Pet adopted successfully!';
            loadPets(); // Reload the pets list
        } else {
            const errorText = await res.text();
            error = errorText || 'Failed to adopt pet';
        }
    }
</script>

<div class="container">
    <div class="box">
        <h1>🐾 Available Pets</h1>

        {#if success}<p style="color: green;">{success}</p>{/if}
        {#if error}<p style="color: red;">{error}</p>{/if}

        <div class="buttons">
            <button class:active={selectedType === ''} on:click={() => selectedType = ''}>All</button>
            <button class:active={selectedType === 'puppy'} on:click={() => selectedType = 'puppy'}>Puppies</button>
            <button class:active={selectedType === 'kitten'} on:click={() => selectedType = 'kitten'}>Kittens</button>
        </div>

        <ul>
            {#each filteredPets as pet}
                <li>
                    <h3>{pet.name} ({pet.type})</h3>
                    <p>Happiness: {pet.happiness}/100</p>
                    <p>Hunger: {pet.hunger}/100</p>
                    <p>Status: {pet.owner === null ? 'Available' : 'Adopted'}</p>

                    {#if !pet.owner}
                        <button on:click={() => adoptPet(pet.id)}>Adopt</button>
                    {:else}
                        <p><i>Already adopted</i></p>
                    {/if}
                </li>
            {/each}
        </ul>
    </div>
</div>

<style>
    /* Center the body content */
    body {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        margin: 0;
        background-color: #f4f4f9; /* Light background color */
    }

    /* Centered content container */
    .container {
        display: flex;
        justify-content: center;
        align-items: center;
        width: 100%;
    }

    /* Box styling */
    .box {
        padding: 2rem;
        border: 2px solid #0077cc; /* Blue border for the box */
        background-color: white;
        border-radius: 8px;
        box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
        max-width: 1000px;
        width: 100%;
        text-align: center;
    }

    /* Title styling with emoji */
    h1 {
        font-size: 2.5rem;
        color: #0077cc;
        margin-bottom: 1rem;
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
        margin-top: 1rem;
    }

    button:hover {
        background-color: #005fa3;
    }

    /* Button active class for selected pet type filter */
    button.active {
        background-color: #005fa3;
    }

    /* List and pet card styling */
    ul {
        list-style: none;
        padding: 0;
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
        gap: 1rem;
    }

    li {
        border: 2px solid #0077cc; /* Blue outline for each pet card */
        padding: 1rem;
        border-radius: 8px;
        background-color: #f9f9f9;
        text-align: left;
    }

    /* Pet information styling */
    h3 {
        color: #0077cc;
        font-size: 1.2rem;
    }

    p {
        font-size: 1rem;
        color: #333;
    }

    /* Error message styling */
    p {
        color: red;
    }

    /* Styling for buttons inside list items */
    .buttons {
        display: flex;
        justify-content: center;
        gap: 0.5rem;
    }
</style>
