<script>
    let { 
        text = "Heading text",
        headingLevel = "2",
    } = $props();
 
    let headingTag = $derived('h' + headingLevel);
    let headingClass = $derived('heading-' + headingLevel);
</script>

<svelte:element this={headingTag} class={headingClass}>
    {text}
</svelte:element>

<style>

    h1, h2, h3, h4, h5, h6 {
        font-family: 'Courier New', Courier, monospace;
        padding: 0.5rem 0 0.5rem 0;
    }

    .heading-1 {
        font-weight: 900;
        font-size: 2.5rem;
    }

    .heading-2 {
        font-weight: 600;
        font-size: 1.8rem;
    }

    .heading-3 {
        font-weight: 600;
        font-size: 1.5rem;
    }
</style>