<script lang="ts">
	import { currentUser } from '$lib/stores';

	let user = $currentUser;
	let message = '';

	const prices = {
		food: 10,
		toy: 15,
		treat: 5
	};

	async function buy(item: 'food' | 'toy' | 'treat') {
		if (!user) {
			message = 'Please login to buy items.';
			return;
		}

		const res = await fetch('/api/shop', {
			method: 'POST',
			headers: { 'Content-Type': 'application/json' },
			body: JSON.stringify({ userId: user.id, item, cost: prices[item] })
		});

		if (res.ok) {
			message = `${item} purchased!`;
			user.budget -= prices[item];
			user.inventory[item] = (user.inventory[item] || 0) + 1;
		} else {
			message = await res.text();
		}
	}
</script>

<div class="container">
    <div class="box">
        <h1>🐾 Pet Shop</h1>

        {#if user}
            <p>Budget: ${user.budget}</p>
            <p>Inventory: 🥩 {user.inventory.food} 🎾 {user.inventory.toy} 🍬 {user.inventory.treat}</p>
            <button on:click={() => buy('food')}>Buy Food - ${prices.food}</button>
            <button on:click={() => buy('toy')}>Buy Toy - ${prices.toy}</button>
            <button on:click={() => buy('treat')}>Buy Treat - ${prices.treat}</button>
        {:else}
            <p>Please login to see your budget and inventory.</p>
        {/if}

        <p>{message}</p>
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

    /* Error message styling */
    p {
        font-size: 1rem;
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
