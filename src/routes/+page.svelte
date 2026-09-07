<script>
    import Heading from "$lib/components/Heading.svelte";
    import Section from "$lib/components/Section.svelte";
</script>

<Section>
    <Heading text="Check your route to Tiptop College" headingLevel="1"></Heading>
    <p>At Tiptop College, we offer a number of dedicated college bus services specifically for local students. You can find information about routes, stops, and timetables below.</p>
</Section>

<Section theme="medium">
    <Heading text="Find your stop"></Heading>
    <p>New student? Not sure which bus stops near you?</p>
    <p>Enter your postcode below to find out if a location close to you is on a Tiptop College bus route.</p>
</Section>

<Section theme="dark">
    <Heading text="All stops"></Heading>
    <p>This is a list of locations currently served by a Tiptop College bus. Click on a stop to navigate to the full timetable.</p>
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
</style>