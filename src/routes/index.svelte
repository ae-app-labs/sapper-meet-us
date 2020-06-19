<script context="module">
    export function preload(page) {
        return this.fetch('https://meetus-76f91.firebaseio.com/meetups.json')
            .then(res => {
                if(!res.ok){
                    throw new Error('Fetching failed')
                }
                return res.json()
            })
            .then(data => {
                const loadedMeetps = []
                console.log(data)
                for(const key in data){
                    loadedMeetps.push({
                        ...data[key],
                        id: key
                    })
                }
                return {fetchedMeetups:loadedMeetps}
                //meetups.setMeetups(loadedMeetps.reverse())
                //isLoading = false
            })
            .catch(err => {
                isLoading = false
                error = err
                console.log(err)
                this.error(500, 'Could not fetch meetups')
            })
    }
</script>

<script>
    import {createEventDispatcher, onMount, onDestroy} from 'svelte'
    import { scale } from "svelte/transition";
    import { flip } from 'svelte/animate';
    import MeetupItem from '../components/meetup/MeetupItem.svelte'
    import MeetupFilter from '../components/meetup/MeetupFilter.svelte'
    import EditMeetup from "../components/meetup/EditMeetup.svelte";
    import Button from "../components/ui/Button.svelte";
    import Error from "../components/ui/Error.svelte";
    import LoadingSpinner from "../components/ui/LoadingSpinner.svelte";
    import meetups from '../meetup-store.js'

    export let fetchedMeetups

    let editMode = null
    let editedId = null
    let isLoading = true
    let error = null
    let favsOnly = false

    $: filteredMeetups = favsOnly ? fetchedMeetups.filter( i => i.isFavourite) : fetchedMeetups

    const dispatch = createEventDispatcher()

    const setFilter = (event) => {
        favsOnly = event.detail === 1
    }

    onMount( () => {
        meetups.setMeetups(fetchedMeetups)    
        isLoading = false    
    })

    const saveMeetup = (event) => {
        editMode = null
        editedId = null
    }

    const cancelEdit = () => {
        editMode = null
        editedId = null
    }

    const startEdit = (event) => {
        editMode = 'edit'
        editedId = event.detail
    }

    const clearError = () => { error = null }

</script>

<style>
    #meetups {
        width: 100%;
        display: grid;
        grid-template-columns: 1fr;
        grid-gap: 1rem;
    }
    #meetup-controls {
        margin: 1rem;
        display: flex;
        justify-content: space-between;
    }

    @media (min-width: 768px) {
        #meetups {
            grid-template-columns: repeat(2, 1fr);
        }
    }

    #no-meetups{
        margin: 1rem;
    }
</style>

<svelte:head>
	<title>All Meetups</title>
</svelte:head>

{#if error}
     <Error message={error.message} on:close={clearError}/>
{/if}

{#if editMode === 'edit'}
    <EditMeetup on:save={saveMeetup} on:close={cancelEdit} id={editedId}/>
{/if}

{#if isLoading}
    <LoadingSpinner />
{:else}
    <section id="meetup-controls">
        <MeetupFilter on:select={setFilter}/>
        <Button  on:click={ startEdit }>New Meetup</Button>
    </section>

    {#if filteredMeetups.length === 0}
        <p id="no-meetups">No meetups found. Start adding some.</p>
    {/if}

    <section id="meetups">
        {#each filteredMeetups as meetup (meetup.id)}
            <div transition:scale animate:flip={{duration:300}}>
                <MeetupItem title={meetup.title}
                    id={meetup.id}
                    subtitle={meetup.subtitle}
                    description={meetup.description}
                    imageUrl={meetup.imageUrl}
                    contact={meetup.contact}
                    address={meetup.address}
                    isFav={meetup.isFavourite}
                    on:edit
                    on:showdetails/>
            </div>
        {/each}
    </section>

{/if}