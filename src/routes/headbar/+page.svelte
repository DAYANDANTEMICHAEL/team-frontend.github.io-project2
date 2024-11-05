<!-- src/routes/headbar/+page.svelte -->
<script lang="ts">
  import { navigate, link } from 'svelte-routing';
  import { onMount } from 'svelte';

  let currentDateTime: string;

  function handleLogout() {
    navigate('/login'); // Navigate to the login page
  }

  onMount(() => {
    const updateDateTime = () => {
      const now = new Date();
      const options: Intl.DateTimeFormatOptions = {
        year: 'numeric',
        month: 'long',
        day: 'numeric',
        hour: 'numeric',
        minute: 'numeric',
        second: 'numeric',
        hour12: true,
      };
      currentDateTime = now.toLocaleString('en-US', options).replace(',', ' at'); // Format the date and time
    };
    updateDateTime();
    const interval = setInterval(updateDateTime, 1000); // Update every second
    return () => clearInterval(interval); // Cleanup on component destroy
  });
</script>

<nav class="bg-green-500 p-4 flex justify-between items-center">
  <div class="text-white text-lg font-bold">
    EVENT Scheduling
  </div>
  <div class="text-white text-lg">
    {currentDateTime} <!-- Display current date and time -->
  </div>
  <button class="text-white font-semibold" on:click={handleLogout}>
    Log Out
  </button>
</nav>
