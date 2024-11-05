<script lang="ts">
  import Headbar from '../headbar/+page.svelte'; // Importing the headbar component\
  import CreateEvent from '../create-event/+page.svelte';
  import { onMount } from 'svelte';
  import '@fortawesome/fontawesome-free/css/all.css';

  interface Event {
    contactNumber: string;
    name: string;
    eventName: string;
    startTime: string;
    endTime: string;
  }

  let showPopup = false; // State to control the popup visibility
  let events: Event[] = []; // Initialize as an empty array
  let selectedEvent: Event | null = null; // State to hold the event being edited
  let confirmDeleteEvent: Event | null = null; // State to hold the event to be deleted
  let showAddEventPopup = false; // State to control the add event popup visibility
  let currentPage = 1; // State to track the current page
  const eventsPerPage = 18; // Number of events to display per page

  function addEvent(event: CustomEvent<Event>) {
    events = [...events, event.detail]; // Add new event to the events array
    showPopup = false; // Close the popup after scheduling
  }

  function togglePopup() {
    showPopup = !showPopup; // Toggle the popup visibility
  }

  function toggleAddEventPopup() {
    showAddEventPopup = !showAddEventPopup; // Toggle the add event popup visibility
  }

  function confirmDelete(event: Event) {
    confirmDeleteEvent = event; // Set the event to be deleted
    showAddEventPopup = false; // Close the add event popup if open
    showPopup = true; // Show the confirmation popup
  }

  function deleteConfirmed() {
    if (confirmDeleteEvent) {
      events = events.filter(e => e !== confirmDeleteEvent); // Remove the event from the events array
      localStorage.setItem('eventData', JSON.stringify(events)); // Update local storage
      confirmDeleteEvent = null; // Reset the confirmation state
    }
    showPopup = false; // Close the confirmation popup
  }

  function cancelDelete() {
    confirmDeleteEvent = null; // Reset the confirmation state
    showPopup = false; // Close the confirmation popup
  }

  function formatDateTime(dateTime: string): string {
    const options: Intl.DateTimeFormatOptions = { 
      year: 'numeric', 
      month: 'long', 
      day: 'numeric', 
      hour: 'numeric', 
      minute: 'numeric', 
      hour12: true 
    };
    return new Date(dateTime).toLocaleString('en-US', options);
  }

  function openEditPopup(event: Event) {
    selectedEvent = event; // Set the selected event for editing
    showPopup = true; // Show the popup
  }

  function nextPage() {
    if (currentPage * eventsPerPage < events.length) {
      currentPage++; // Increment the current page
    }
  }

  function previousPage() {
    if (currentPage > 1) {
      currentPage--; // Decrement the current page
    }
  }

  // Load events from local storage on component mount
  onMount(() => {
    const storedEvents = localStorage.getItem('eventData');
    if (storedEvents) {
      try {
        const parsedEvents = JSON.parse(storedEvents);
        // Ensure parsedEvents is an array
        if (Array.isArray(parsedEvents)) {
          events = parsedEvents.sort((a, b) => new Date(a.startTime).getTime() - new Date(b.startTime).getTime()); // Sort events by start time
        } else {
          console.error('Stored data is not an array:', parsedEvents);
        }
      } catch (error) {
        console.error('Error parsing stored events:', error);
      }
    }
  });
</script>
<Headbar />
<div class="flex flex-col h-[calc(100vh-64px)] overflow-hidden p-4">
  <button class="bg-blue-500 text-white px-4 py-2 rounded mb-4 hover:bg-blue-700" on:click={toggleAddEventPopup}>Add Event</button>
  
  {#if showAddEventPopup}
    <div class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50 z-50">
      <div class="bg-white p-4 rounded relative">
        <button on:click={toggleAddEventPopup} class="absolute top-2 right-2 text-gray-500 hover:text-gray-800">
          &times;
        </button>
        <CreateEvent on:schedule={addEvent} />
      </div>
    </div>
  {/if}
  
  {#if confirmDeleteEvent}
    <div class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50 z-50">
      <div class="bg-white p-4 rounded relative">
        <h2 class="text-lg font-bold">Are you sure you want to delete it?</h2>
        <div class="mt-4 flex justify-center">
          <button on:click={deleteConfirmed} class="bg-red-500 text-white px-4 py-2 rounded mr-2">Yes</button>
          <button on:click={cancelDelete} class="bg-gray-300 text-gray-700 px-4 py-2 rounded">No</button>
        </div>
      </div>
    </div>
  {/if}
  
  <table class="min-w-full border-collapse border border-gray-300">
    <thead>
      <tr class="bg-gray-100">
        <th class="border border-gray-300 px-4 py-2">Email</th>
        <th class="border border-gray-300 px-4 py-2">Contact</th>
        <th class="border border-gray-300 px-4 py-2">Event Name</th>
        <th class="border border-gray-300 px-4 py-2">Start Time</th>
        <th class="border border-gray-300 px-4 py-2">End Time</th>
        <th class="border border-gray-300 px-4 py-2">Actions</th>
      </tr>
    </thead>
    <tbody>
      {#each events.slice((currentPage - 1) * eventsPerPage, currentPage * eventsPerPage) as event}
        <tr class={new Date(event.startTime) < new Date() ? 'bg-gray-200' : ''}>
          <td class="border border-gray-300 px-4 py-2 text-center">{event.name}</td>
          <td class="border border-gray-300 px-4 py-2 text-center">{event.contactNumber}</td>
          <td class="border border-gray-300 px-4 py-2 text-center">{event.eventName}</td>
          <td class="border border-gray-300 px-4 py-2 text-center">{formatDateTime(event.startTime)}</td>
          <td class="border border-gray-300 px-4 py-2 text-center">{formatDateTime(event.endTime)}</td>
          <td class="border border-gray-300 px-4 py-2 text-center">
            <button class="text-red-500 hover:text-red-700" on:click={() => confirmDelete(event)}>
              <i class="fas fa-trash"></i> <!-- FontAwesome Delete Icon -->
              Delete
            </button>
          </td>
        </tr>
      {/each}
      {#if events.length === 0}
        <tr>
          <td class="border border-gray-300 px-4 py-2 text-center" colspan="6">No events available</td>
        </tr>
      {/if}
    </tbody>
  </table>
</div>

<!-- Fixed Navigation Buttons at the very end -->
<div class="fixed bottom-0 left-0 right-0 bg-white shadow-md p-4 flex justify-between">
  <button class="bg-gray-300 text-gray-700 px-4 py-2 rounded hover:bg-gray-400" on:click={previousPage} disabled={currentPage === 1}>
    Back
  </button>
  <p>Page {currentPage} of {Math.max(1, Math.ceil(events.length / eventsPerPage))}</p>
  <button class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-700" on:click={nextPage} disabled={currentPage * eventsPerPage >= events.length}>
    Next
  </button>
</div>
