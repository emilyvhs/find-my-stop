<script>
    import Heading from "$lib/components/Heading.svelte";
    import Section from "$lib/components/Section.svelte";
    import { stops } from '$lib/data/route-data.json';

    let allStops = [];
    let allRoutes = [];  

    stops.forEach(stop => {
        allStops.push({name: stop.name, slug: stop.slug});
        allRoutes.push({name: stop.route_name, slug: stop.route_slug});        
    });

    let alphabetisedStops = allStops.sort((a, b) => a.name.localeCompare(b.name));
    let routes = [...new Set(allRoutes.map(JSON.stringify))].map(JSON.parse);
    
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
    {#each routes as route}
        <div class="flex flex-col items-center">
            <div id={route.slug} class="w-[90%] my-4">
                <div class="place-self-start">
                     <Heading text={route.name} headingLevel="3"></Heading>
                </div>
            </div>

            <div class="w-[90%]">
                <table>
                    <thead>
                        <tr>
                            <th>Location</th>
                            <th>Pick up time (AM)</th>
                            <th>Address</th>
                            <th>Drop off time (PM)</th>
                        </tr>
                    </thead>
                    <tbody>
                        {#each stops as stop}
                            {#if stop.route_name === route.name}
                                <tr id={stop.slug}>
                                    <td>{stop.name}</td>
                                    <td>{stop.morning_time}</td>
                                    <td><a href="{stop.google_link}" target="_blank">{stop.address}</a></td>
                                    <td>{stop.evening_time}</td>
                                </tr>
                            {/if}
                        {/each}
                    </tbody>
                </table>
            </div>
        </div>
    {/each}
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

    table {

        text-align: left;
        border-collapse: collapse;
        max-width: 100%;
        table-layout: fixed;
        width: 100%;

        thead {
            background-color: var(--color-teal-800);
            color: var(--color-teal-50);   
        }

        tbody tr:nth-child(odd){
            background-color: var(--color-teal-100);
        }

        tbody tr:nth-child(even){
            background-color: var(--color-teal-200);
        }        

        th, td {
            padding: 0.5rem;
        }
    }
    
</style>