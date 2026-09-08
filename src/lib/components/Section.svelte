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

        :global {

            a {
                color: var(--color-fuchsia-600);
                text-decoration: underline;
                cursor: pointer;
            }

            a:hover {
                color: var(--color-fuchsia-800);
            }
        } 
    }

    .medium-section {
        background-color: var(--color-teal-200);
    }

    .dark-section {

        background-color: var(--color-teal-800);
        color: var(--color-teal-50);

        :global {

            a {
                color: var(--color-fuchsia-200);
                text-decoration: underline;
                cursor: pointer;
            }

            a:hover {
                color: var(--color-fuchsia-300);
            }

            li {
                list-style-type: circle;
                list-style-position: inside;            
            }
        }         
    }
</style>