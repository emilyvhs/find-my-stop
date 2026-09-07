<script>
    let {
        theme = "light"
    } = $props();

    let sectionClass = $derived(theme + '-section');

</script>

<section class={sectionClass}>
    <slot />
</section>

<style>

    section {
        padding: 3rem;
    }

    .light-section {
        background-color: var(--color-teal-50);
    }

    .medium-section {
        background-color: var(--color-teal-200);
    }

    .dark-section {
        background-color: var(--color-teal-800);
        color: var(--color-teal-50);
    }
</style>