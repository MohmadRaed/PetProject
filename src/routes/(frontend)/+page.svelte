<script lang="ts">
	import { currentUser } from '$lib/stores';

	$: user = $currentUser;
	const prices = { food: 10, toy: 15, treat: 5 };
	let message = '';

	async function buy(item: 'food' | 'toy' | 'treat') {
		if (!user) {
			message = 'Please log in to buy items.';
			return;
		}

		const res = await fetch('/api/shop', {
			method: 'POST',
			body: JSON.stringify({
				userId: user.id,
				item,
				cost: prices[item]
			}),
			headers: {
				'Content-Type': 'application/json'
			}
		});

		if (res.ok) {
			message = `Successfully purchased ${item}`;
			user.budget -= prices[item];
			user.inventory[item] = (user.inventory[item] || 0) + 1;
		} else {
			message = await res.text();
		}
	}
</script>

<div class="container">
    <div class="box">
        <h1>🐾 Raed's Pet Shop</h1>
        <p>Please log in to purchase items.</p>
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
        border: 2px solid #0077cc;
        background-color: white;
        border-radius: 8px;
        box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
        max-width: 500px;
        width: 100%; /* Ensures the box doesn't overflow */
        text-align: center;
    }

    h1 {
        font-size: 2rem;
        color: #0077cc;
    }

    p {
        font-size: 1rem;
        color: #333;
    }
</style>
