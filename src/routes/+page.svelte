<script lang="ts">
	import { redirect } from '@sveltejs/kit';
	import Login from './login/+page.svelte';
	export async function load({ redirect }: { redirect: (status: number, location: string) => void }) {
		return redirect(302, '/login'); // Redirect to /login
	}
</script>

<!-- You can leave this empty or add a message if needed --> 