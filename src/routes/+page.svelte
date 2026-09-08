<script>
    import Heading from "$lib/components/Heading.svelte";
    import Section from "$lib/components/Section.svelte";
    import { stops } from '$lib/data/route-data.json';

    let alphabetisedStops = [];
    stops.forEach(stop => {
        alphabetisedStops.push({name: stop.name, slug: stop.slug});
    });
    alphabetisedStops.sort((a, b) => a.name.localeCompare(b.name));

    console.log(alphabetisedStops)
    
</script>

<Section>
    <Heading text="Check your route to Tiptop College" headingLevel="1"></Heading>
    <p>At Tiptop College, we offer a number of dedicated college bus services specifically for local students. You can find information about routes, stops, and timetables below.</p>
</Section>

<Section theme="medium">
    <Heading text="Find your stop"></Heading>
    <p>New student? Not sure which bus stops near you?</p>
    <p>Enter your postcode below to find out if a location close to you is on a Tiptop College bus route.</p>

    <form class="mt-8">
        <input type="text" placeholder="e.g. DE6 2EB" class="rounded-sm" />
        <input type="submit" value="Find your stop" class="p-2 cursor-pointer border border-fuchsia-600 rounded-sm bg-fuchsia-600 hover:bg-fuchsia-800 hover:border-fuchsia-800 text-teal-50" />
    </form>
</Section>

<Section theme="dark">
    <Heading text="All stops"></Heading>
    <p>This is a list of locations currently served by a Tiptop College bus. Click on a stop to navigate to the full timetable.</p>
    <ul class="stop-list p-4">
        {#each alphabetisedStops as stop}
        <li><a href="#{stop.slug}">{stop.name}</a></li>
        {/each}
    </ul>
</Section>

<Section>
    <Heading text="Timetables"></Heading>
</Section>

<style>

    :global {

        body {     
            font-family: Verdana, Geneva, Tahoma, sans-serif;
        }
      
    }

    .stop-list {
        display: grid;
        grid-template-rows: repeat(5, 1fr);
        grid-template-columns: auto;
        grid-auto-flow: column;

        @media screen and (max-width: 767px) {
            grid-template-rows: repeat(10, 1fr);
        }

        @media screen and (max-width: 479px) {
            grid-template-rows: auto;
            grid-template-columns: 1fr;
            grid-auto-flow: row;
        }
    }
    
</style>