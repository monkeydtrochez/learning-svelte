<script>
  import meetups from './Meetups/meetups-store.js';
  import Header from "./UI/Header.svelte"
  import MeetupGrid from "./Meetups/MeetupGrid.svelte";
  import EditMeetup from "./Meetups/EditMeetup.svelte";
  import MeetupDetail from "./Meetups/MeetupDetail.svelte";
  import LoadingSpinner from "./UI/LoadingSpinner.svelte";
  import Error from "./UI/Error.svelte";
  import {onMount} from 'svelte';
  
  let editMode = null;
  let editId;
  let page = "overview";
  let pageData = {};
  let isLoading = true;
  let errorMessage;

  onMount(() => {
    fetch("https://monkey-svelte-course-default-rtdb.europe-west1.firebasedatabase.app/meetups.json")
    .then(res => {
      if(!res.ok) {
        throw new Error("Something went wrong try again later!");
      }
      return res.json();
    })
    .then(data => {
      const loadedMeetups = [];
      for (const key in data) {
        loadedMeetups.push({
          ...data[key],
          id: key
        });
      }
      setTimeout(() => { // Just to visualize longer loagind time
        meetups.setMeetups(loadedMeetups.reverse());
        isLoading = false;
      }, 1500);
    })
    .catch(err => {
      errorMessage = err;
      console.log(err)
      isLoading = false;
    });
  });
  
  function saveMeetup() {
    editMode = null;
    editId = null;
  }
  
  function cancelEdit(){
    editMode = null;
    editId = null;
  }
  
  function showDetails(event) {
    page = "details";
    pageData.id = event.detail;
  }
  
  function closeDetails() {
    page = "overview";
    pageData = {};
  }
  
  function editMeetup(event) {
    editMode = "edit"
    editId = event.detail;
  }

  function clearMessage() {
    errorMessage = null;
  }
</script>

<style>
  main {
    margin-top: 5rem;
  }
</style>

<Header/>

{#if errorMessage}
  <Error errorMessage={errorMessage.message} on:cancel={clearMessage}/>
{/if}

<main>
  {#if page === "overview"}
  {#if editMode === 'edit'}
  <EditMeetup id={editId} on:save={saveMeetup} on:cancel={cancelEdit}/>
  {/if}
  {#if isLoading}
  <LoadingSpinner />
  {:else}
  <MeetupGrid 
  meetups={$meetups} 
  on:showDetails={showDetails} 
  on:edit={editMeetup} 
  on:add={() => {editMode = "edit"}}/>
  {/if}
  {:else if page === "details"}
  <MeetupDetail id={pageData.id} on:close={closeDetails}/>
  {/if}
  
</main>
