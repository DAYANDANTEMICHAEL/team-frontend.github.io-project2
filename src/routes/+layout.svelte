<script lang="ts">
	import { Router, Route, navigate } from 'svelte-routing';
	import '../app.css';
	import 'flowbite/dist/flowbite.css';
	import Login from './login/+page.svelte';
	import Register from './register/+page.svelte';
	import Dashboard from './dashboard/+page.svelte';
	import { onMount } from 'svelte';

	// Add this function to handle redirection
	function redirectToLogin() {
		navigate('/login');
	}

	// Call the redirect function on component mount
	onMount(() => {
		redirectToLogin();
	});
</script>

<Router>
	<slot />
	<Route path="/login" component={Login} />
	<Route path="/register" component={Register} />
	<Route path="/dashboard" component={Dashboard} />
</Router>

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css" integrity="sha384-k6RqeWeci5ZR/Lv4MR0sA0FfDOM6g0g5z5e5e5e5e5e5e5e5e5e5e5e5e5e5e5e" crossorigin="anonymous">

