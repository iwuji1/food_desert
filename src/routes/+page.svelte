<script>
    import MAP from '$lib/components/map.svelte'
    import { gsap } from "gsap";

    import { onMount } from 'svelte';

    let mapComponent;

    onMount(() => {
        gsap.registerPlugin(ScrollTrigger);

        const sections = gsap.utils.toArray("section:not(:first-child)")
        const allsections = gsap.utils.toArray("section")

        const mapfunctions = [mapComponent.flyOut, mapComponent.flyToLondon, mapComponent.plotPoints, mapComponent.plotRadius, mapComponent.flyToLondon, mapComponent.flyOut]

        var tl = gsap.timeline({
            scrollTrigger: {
                trigger: ".map-ani",
                start: "top top",
                end: "bottom bottom",
                scrub: 1,
                pin: ".map"
            }
        })

        sections.forEach((unit, i) => {
            let headline = unit.querySelector("h1");
            ScrollTrigger.create({
                trigger: headline,
                start: "top 50%",
                end: "top 50%",
                scrub: true,
                onEnter: () => mapfunctions[i+1](),
                onEnterBack: () => mapfunctions[i](),
            })
        })

        
    })
</script>

<svelte:head>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/gsap.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/ScrollTrigger.min.js"></script>
</svelte:head>
<style>
    .intro {
        display: flex;
        height: 100vh;
        width: 100vw;
        justify-content: center;
        text-align: center;
        align-items: center;
        flex-direction: column;
    }

    .end {
        display: flex;
        height: 100vh;
        width: 100vw;
        justify-content: center;
        text-align: center;
        align-items: center;
        flex-direction: column;
    }
    h1 {
        align-self: center;
        font-family: sans-serif;
        font-size: 5em;
    }

    p {
        align-self: center;
        font-family: sans-serif;
    }

    .map-ani {
        display: flex;
        flex-direction: column;
    }

    .ContentSection {
        z-index: 5;
        height: 100vh;
        width: 50%;
    }

</style>

<div class="intro">
    <h1>Food Deserts</h1>
    <p>Food Deserts in London</p>
</div>

<div class="map-ani">
    <section class="ContentSection">
        <h1>Check 1</h1>
    </section>
    <section class="ContentSection">
        <h1>Check 2</h1>
    </section>
    <section class="ContentSection">
        <h1>Check 3</h1>
    </section>
    <section class="ContentSection">
        <h1>Check 4</h1>
    </section>
    <section class="ContentSection">
        <h1>Check 5</h1>
    </section>
    <section class="ContentSection">
        <h1>Check 6</h1>
    </section>
    <MAP bind:this={mapComponent}/>
</div>

<div class="end">
    <h1>The End</h1>
</div>


