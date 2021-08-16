<script>
    import {createEventDispatcher} from 'svelte';
    import MeetupItem from "./MeetupItem.svelte";
    import MeetupFilter from "./MeetupFilter.svelte"
    import Button from "../UI/Button.svelte";
    import { scale } from 'svelte/transition';
    import { flip } from 'svelte/animate';
    
    const dispatch = createEventDispatcher();
    
    export let meetups;
    let showOnlyFavorites = false;
    
    $: filteredMeetups = showOnlyFavorites ? meetups.filter(x => x.isFavorite) : meetups;
    
    function filterGrid(event) {
        showOnlyFavorites = event.detail === 1;
    }
</script>

<style>
    #meetups {
        width: 100%;
        display: grid;
        grid-template-columns: 1fr;
        grid-gap: 1rem;
    }

    #no-meetups {
        margin: 1rem;
    }
    
    #meetups-control {
        margin: 1rem;
        display: flex;
        justify-content: space-between;
    }
    
    @media (min-width: 768px) {
        #meetups {
            grid-template-columns: repeat(2, 1fr);
        }
    }
</style>

<section id="meetups-control">
    <MeetupFilter on:select={filterGrid}/>
    <Button on:click={() => dispatch('add')} >New meetup</Button>
</section>

{#if filteredMeetups.length === 0}
    <p id="no-meetups">There is now meetups, go ahead and add meetups.</p>
{/if}
<section id="meetups">
    {#each filteredMeetups as item (item.id)}
    <div transition:scale animate:flip={{duration: 300}}>
        <MeetupItem
        id={item.id}
        title={item.title}
        subtitle={item.subtitle}
        description={item.description}
        image={item.imageUrl}
        address={item.address}
        isFavorite={item.isFavorite}
        on:edit
        on:showDetails
        />
    </div>
    {/each}
</section>