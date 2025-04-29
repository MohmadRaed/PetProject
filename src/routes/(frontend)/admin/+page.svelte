<script lang="ts">
	import { onMount } from 'svelte';
	import { currentUser } from '$lib/stores';
	import { goto } from '$app/navigation';

	let name = 'New Pet';
	let type = 'puppy';
	let error = '';
	let success = '';

	$: user = $currentUser;

	onMount(() => {
		if (!user) {
			goto('/login');
			return;
		}

		if (!user.isAdmin) {
			goto('/');
			return;
		}
	});

	async function addPet() {
		if (!user || !user.isAdmin) {
			error = 'You need admin permission to add pets';
			return;
		}

		const pet = {
			id: Date.now(),
			name,
			type,
			hunger: 50,
			happiness: 50,
			adoptionStatus: false,
			owner: null
		};

		const res = await fetch('/api/pets', {
			method: 'POST',
			body: JSON.stringify(pet),
			headers: {
				'Content-Type': 'application/json'
			}
		});

		if (res.ok) {
			success = 'Pet added successfully!';
			name = 'New Pet'; // Reset form
			type = 'puppy';
		} else {
			const errorText = await res.text();
			error = errorText || 'Failed to add pet';
		}
	}
</script>

<div class="container">
    <div class="box">
        <h1>🐾 Add a New Pet</h1>

        {#if success}<p style="color: green;">{success}</p>{/if}
        {#if error}<p style="color: red;">{error}</p>{/if}

        <form on:submit|preventDefault={addPet}>
            <div>
                <label for="name">Pet Name</label>
                <input id="name" type="text" bind:value={name} placeholder="Enter pet name" required />
            </div>

            <div>
                <label for="type">Pet Type</label>
                <select id="type" bind:value={type}>
                    <option value="puppy">Puppy</option>
                    <option value="kitten">Kitten</option>
                </select>
            </div>

            <button type="submit">Add Pet</button>
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
        background-color: #f4f4f9; /* Light background */
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

    /* Input and select styling */
    input, select {
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
        margin-top: 1rem;
        width: 100%;
    }

    button:hover {
        background-color: #005fa3;
    }

    /* Error and success message styling */
    p {
        font-size: 1rem;
        color: #333;
    }
</style>
