<script>
    import Heading from "$lib/components/Heading.svelte";
    import Section from "$lib/components/Section.svelte";
    import { stops } from '$lib/data/route-data.json';
    import { onMount } from "svelte";
    import { env } from "$env/dynamic/public";
    
    let googleMapsKey = env.GOOGLE_MAPS_KEY;

    let allStops = [];
    let allRoutes = [];  

    stops.forEach(stop => {
        allStops.push({name: stop.name, slug: stop.slug});
        allRoutes.push({name: stop.route_name, slug: stop.route_slug});        
    });

    let alphabetisedStops = allStops.sort((a, b) => a.name.localeCompare(b.name));
    let routes = [...new Set(allRoutes.map(JSON.stringify))].map(JSON.parse);
  
    let geocoder = $state();
    let postcode = $state();
    let lat = $state();
    let lng = $state();

    // Default values to show what results card looks like - remove in production
    let closestStop = $state({name: "Tollgate Cottage", slug: "tollgate-cottage", route_name: "Mayfield"});
    let secondStop = $state({name: "Hall Lane", slug: "hall-lane", route_name: "Mayfield"});
    let thirdStop = $state({name: "Hermitage Lane", slug: "hermitage-lane", route_name: "Mayfield"}); 

    // Call Google Maps Geocoder API on mount and return a new instance of the Geocoder class
    onMount(async () => {
        (g=>{var h,a,k,p="The Google Maps JavaScript API",c="google",l="importLibrary",q="__ib__",m=document,b=window;b=b[c]||(b[c]={});var d=b.maps||(b.maps={}),r=new Set,e=new URLSearchParams,u=()=>h||(h=new Promise(async(f,n)=>{await (a=m.createElement("script"));e.set("libraries",[...r]+"");for(k in g)e.set(k.replace(/[A-Z]/g,t=>"_"+t[0].toLowerCase()),g[k]);e.set("callback",c+".maps."+q);a.src=`https://maps.${c}apis.com/maps/api/js?`+e;d[q]=f;a.onerror=()=>h=n(Error(p+" could not load."));a.nonce=m.querySelector("script[nonce]")?.nonce||"";m.head.append(a)}));d[l]?console.warn(p+" only loads once. Ignoring:"):d[l]=(f,...n)=>r.add(f)&&u().then(()=>d[l](f,...n))})({
            key: googleMapsKey,
            v: "weekly",           
        });

        const { Geocoder } = await google.maps.importLibrary("geocoding");
        geocoder = new google.maps.Geocoder();
    });

    // Take the input postcode and request lat/lng coordinates from Geocoder
    // If request is OK, set lat/lng and call calculateDistance()
    const geocodePostcode = async(e) => {
        e.preventDefault();
        closestStop = undefined;
        
        // Error text for invalid postcode - hide again if already being displayed
        let postcodeError = document.getElementById("postcode-error");
        if (!postcodeError.classList.contains("hidden")) {
            postcodeError.classList.add("hidden");
        }

        // Error text for other Geocoder status errors (usually related to connection or API https://developers.google.com/maps/documentation/javascript/reference/geocoder#GeocoderStatus) - hide again if already being displayed
        let connectionError = document.getElementById("connection-error");
        if (!connectionError.classList.contains("hidden")) {
            connectionError.classList.add("hidden");
        };

        // Error text for distance - no stop within 10km - hide again if already being displayed
        let distanceError = document.getElementById("distance-error");
        if (!distanceError.classList.contains("hidden")) {
            distanceError.classList.add("hidden");
        };
        
        geocoder.geocode({ 'address' : postcode }, function(results, status) {
            if (status == 'OK') {            
                let latLgnObject = results[0].geometry.location;
                lat = latLgnObject.lat();
                lng = latLgnObject.lng();
                calculateDistance(lat, lng);    
                
            } else if (status == 'INVALID_REQUEST') {
                postcodeError.classList.remove("hidden");                

            } else {
                connectionError.classList.remove("hidden");
            };
        });
    };

    // Convert coordinates from degrees to radians (to account for curve of the Earth!)
    function degToRad(deg) {
        let rad = (deg * Math.PI)/180;
        return rad;
    };

    // Calculate distance between two coordinates
    function calculateDistance(startLatCoords, startLngCoords) {

        // Radius of the Earth (km)
        let radius = 6371;

        let startLat = degToRad(startLatCoords);
        let startLng = degToRad(startLngCoords);

        let distance;
        let stopsSortedByDistance;

        stops.forEach(stop => {
            let destLat = degToRad(stop.lat);
            let destLng = degToRad(stop.lng);

            // Haversine Formula - compare distance between two points on a globe //
            distance = Math.acos(
                Math.sin(startLat) * Math.sin(destLat) +
                Math.cos(startLat) * Math.cos(destLat) *
                Math.cos(startLng - destLng)
            ) * radius;

            // Add a new distance property to each stop object in stops
            stop.distance = distance;            
        });

        // Sort by distance
        stopsSortedByDistance = stops.sort((a, b) => a.distance - b.distance);

        // Check first stop is closer than 10km and set closestStop - if not, display error
        let distanceError = document.getElementById("distance-error");
        if (stopsSortedByDistance[0].distance < 10) {            
            closestStop = stopsSortedByDistance[0];
            secondStop = stopsSortedByDistance[1];
            thirdStop = stopsSortedByDistance[2];            
        } else {
            distanceError.classList.remove("hidden");
        };
    };
    
</script>

<!-- Intro -->
<Section>
    <Heading text="Check your route to Tiptop College" headingLevel="1"></Heading>
    <p>At Tiptop College, we offer a number of dedicated college bus services specifically for local students. You can find information about routes, stops, and timetables below.</p>
</Section>

<!-- Postcode checker -->
<Section theme="medium">
    <Heading text="Find your stop"></Heading>
    <p>New student? Not sure which bus stops near you?</p>
    <p>Enter your postcode below to find out if a location close to you is on a Tiptop College bus route.</p>

    <div class="flex flex-col md:flex-row gap-4">
    
        <div>
            <form class="mt-8">
                <input type="text" placeholder="e.g. DE6 2EB" bind:value={postcode} class="rounded-sm" />
                <input type="submit" value="Find your stop" onclick={geocodePostcode} class="p-2 cursor-pointer border border-fuchsia-600 rounded-sm bg-fuchsia-600 hover:bg-fuchsia-800 hover:border-fuchsia-800 text-teal-50" />       
            </form>

            <p id="distance-error" class="mt-2 text-sm text-red-600 error hidden">Sorry, this area is not currently served by any of our Tiptop College bus routes.</p>
            <p id="postcode-error" class="mt-2 text-sm text-red-600 error hidden">Unable to check this location - please enter a valid address or postcode.</p>
            <p id="connection-error" class="mt-2 text-sm text-red-600 error hidden">Connection error - this tool is currently unavailable. Please try again later.</p>
        </div>

        {#if closestStop != undefined}                
            <div class="mt-2 p-4 md:w-[40%] rounded-sm border-2 border-teal-400 bg-teal-800 text-teal-50">                
                <p class="lc-heading-xs">Your closest stop is <strong><a href="#{closestStop.slug}">{closestStop.name}</a></strong> on the {closestStop.route_name} service.</p>
                <p>Other nearby stops include <a href="#{secondStop.slug}">{secondStop.name}</a> ({secondStop.route_name}) and <a href="#{thirdStop.slug}">{thirdStop.name}</a> ({thirdStop.route_name}).</p>                
            </div>
        {/if} 
    </div>
</Section>

<!-- Stops -->
<Section theme="dark">
    <Heading text="All stops"></Heading>
    <p>This is a list of locations currently served by a Tiptop College bus. Click on a stop to navigate to the full timetable.</p>
    <ul class="stop-list p-4">
        {#each alphabetisedStops as stop}
        <li><a href="#{stop.slug}">{stop.name}</a></li>
        {/each}
    </ul>
</Section>

<!-- Timetables -->
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
        
        a {
            text-decoration: underline;
            cursor: pointer;
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