<script context="module">
  export function preload(page){
    const meetupId = page.params.id

    return this.fetch(`https://meetus-76f91.firebaseio.com/meetups/${meetupId}.json`)
    .then(res => {
        if(!res.ok){
            throw new Error('Fetching failed')
        }
        return res.json()
    })
    .then( meetupData => {
      return {loadedMeetup: {...meetupData, id: meetupId} }
    })
    .catch( (err) => {
      this.error(404, 'Meetup item not found')
    })
  }
</script>

<script>
    import {onDestroy, createEventDispatcher} from 'svelte'
    import meetups from '../meetup-store.js'
    import Button from '../components/ui/Button.svelte'

    export let loadedMeetup;
  // -M9uKEt62S_xGg3lJDyr
</script>

<style>
section {
  margin-top: 4rem;
}

.image {
  width: 100%;
  height: 25rem;
}

img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.image {
  background: #e7e7e7;
}

.content {
  text-align: center;
  width: 80%;
  margin: auto;
}

h1 {
  font-size: 2rem;
  font-family: 'Roboto Slab', sans-serif;
  margin: 0.5rem 0;
}

h2 {
  font-size: 1.25rem;
  color: #6b6b6b;
}

p {
  font-size: 1.5rem;
}

</style>

<section>
    <div class="image">
        <img src="{loadedMeetup.imageUrl}" alt="" class="">
    </div>
    <div class="content">
        <h1>{loadedMeetup.title}</h1>
        <h2>{loadedMeetup.subtitle} - {loadedMeetup.address}</h2>
        <p>{loadedMeetup.description}</p>
        <Button href="mailto{loadedMeetup.contact}">Contact E-Mail</Button>
        <Button mode="outline" href="/">Close</Button>
    </div>
</section>