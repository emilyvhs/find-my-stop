<script>
    let { 
        text,
        headingLevel = '2',
        } = $props();
 
    let headingTag = $derived('h' + headingLevel);
</script>

<svelte:element this={headingTag}>
    {text}
</svelte:element>