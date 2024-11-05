<script lang="ts">
	import { onMount } from 'svelte';
	import { navigate, link } from 'svelte-routing';

	let email = 'user@example.com';
	let password = 'password';

	function handleLogin() {
		// ... existing login logic ...
		navigate('/dashboard'); // Redirect to dashboard after login
	}

	function handleSubmit() {
		handleLogin(); // Call handleLogin when the form is submitted
	}
</script>

<div class="flex min-h-screen">
    <div class="flex-1 bg-green-800 flex items-center justify-center">
        <img src="./image1.png" alt="Fallback description if image fails to load" class="max-w-full h-auto" aria-hidden="true" />
    </div>
    <div class="flex-1 flex items-center justify-center">
        <form on:submit|preventDefault={handleSubmit} class="p-6 w-2/3">
            <h2 class="text-2xl font-bold mb-4 text-center">Log In</h2>
            <div class="mb-4">
                <label for="email" class="block text-sm font-medium text-gray-700">Email</label>
                <input type="email" id="email" bind:value={email} required class="mt-1 block w-full p-2 border border-gray-300 rounded" />
            </div>
            <div class="mb-4">
                <label for="password" class="block text-sm font-medium text-gray-700">Password</label>
                <input type="password" id="password" bind:value={password} required class="mt-1 block w-full p-2 border border-gray-300 rounded" />
            </div>
            <button type="submit" class="w-full bg-green-500 text-white p-2 rounded hover:bg-green-600" on:click={handleLogin}>Log In</button>
            <p class="mt-4 text-center">Don't Have Any Account? <a href="/register" class="text-red-500" use:link>Register</a></p>
        </form>
    </div>
</div>
