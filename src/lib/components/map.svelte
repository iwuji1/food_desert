<script>
    import { onMount, onDestroy } from 'svelte';
    import mapboxgl from "mapbox-gl"
    import 'mapbox-gl/dist/mapbox-gl.css';

    import * as d3 from 'd3'
    import { scaleBand, scaleLinear } from "d3-scale";
    import Tooltip from "$lib/components/map_tooltip.svelte";

    let map;
    let mapContainer;
    let lng, lat, zoom;
    let pixelPerKm;
    let hoverData;
    let mapx;
    let mapy;

    //London
    lng = -0.1276;
    lat = 51.5072;
    zoom = 12;

    let markers = [];
    let locations = [];

    let initialState = { lng, lat, zoom };

    const MAPBOX_ACCESS_TOKEN = "pk.eyJ1Ijoib2Jpd3VqaSIsImEiOiJja3B3ZHdvenkwMHV4MnFucHc2YW9tcHcyIn0.wI9Kn7vACQdq61dnjStghg"

    // Define colors for each store type
    const stores = ["McDonald's", "Morleys", "Poundland", "Waitrose", "Lidl", "Pret A Manger"];
    $:cScale = d3.scaleOrdinal().domain(stores).range(["#CF240A","#e8dbcb", "#007E88", "#578626", "#015AA2", "#FEE44D"])


    async function loadData() {
    const response = await fetch("/london_food_desert_stores.json");
    locations = await response.json();
    console.log(locations);
    // loadmarks()
}

function loadmarks() {
    locations.forEach((item) => {

        const marker = new mapboxgl.Marker().setLngLat({lng: item.Longitude, lat: item.Latitude}).addTo(map);
        markers.push(marker)
    });
}

  function updateData() {
        zoom = map.getZoom();
        lng = map.getCenter().lng;
        lat = map.getCenter().lat;
    }


  onMount(() => {
    loadData()

    map = new mapboxgl.Map({
        container: mapContainer,
        accessToken: MAPBOX_ACCESS_TOKEN,
        center: [initialState.lng, initialState.lat],
        zoom: initialState.zoom,
        projection: 'equalEarth',
        interactive: false
    });

    map.on('move', () => {
        updateData();
    })

    onDestroy(() => {
    map.remove();
  })

  map.addControl(new mapboxgl.ScaleControl());

  const scaleBar = document.querySelector(".mapboxgl-ctrl-scale");

  if (scaleBar) {
        const scaleDistanceKM = parseFloat(scaleBar.innerHTML);
        const scaleWidthPx = scaleBar.clientWidth;

        pixelPerKm = scaleWidthPx / scaleDistanceKM;
    }

  });

  function handleReset() {
    map.flyTo({
      center: [initialState.lng, initialState.lat],
      zoom: initialState.zoom,
      essential: true
    });
  }
</script>

<style>
.map {
        position: absolute;
        width: 100%;
        height: 100vh;
    }
    .sidebar {
      background-color: rgb(35 55 75 / 90%);
      color: #fff;
      padding: 6px 12px;
      font-family: monospace;
      z-index: 1;
      position: absolute;
      top: 12%;
      left: 0;
      margin: 12px;
      border-radius: 4px;
    }
    
    .reset-button {
    position: absolute;
    top: 13.5%;
    z-index: 1;
    left: 25%;
    padding: 4px 10px;
    border-radius: 10px;
    cursor: pointer;
}

svg {
    position: absolute;
    z-index: 1;
}


</style>

<div class="map" bind:this={mapContainer}></div>
<svg width= "100%" height= "100%">
    {#each locations as d}
    {#if map}
    <circle 
    class="store-radius"
    id = "radius"
    cx={map.project(new mapboxgl.LngLat(d.Longitude, d.Latitude)).x}
    cy={map.project(new mapboxgl.LngLat(d.Longitude, d.Latitude)).y}
    r={pixelPerKm*3}
    fill = {cScale(d.Store)}
    opacity = "0.2"
    />
    {/if}
{/each}
    {#each locations as d}
        {#if map}
        <circle 
        class="map-dots"
        id = {d.Store}
        cx={map.project(new mapboxgl.LngLat(d.Longitude, d.Latitude)).x}
        cy={map.project(new mapboxgl.LngLat(d.Longitude, d.Latitude)).y}
        r="5"
        fill = {cScale(d.Store)}
        onmouseover ={() => {
            hoverData = d
            mapx = map.project(new mapboxgl.LngLat(d.Longitude, d.Latitude)).x
            mapy = map.project(new mapboxgl.LngLat(d.Longitude, d.Latitude)).y
            }}
        />
        {/if}
    {/each}
</svg>
{#if hoverData}
    <Tooltip data={hoverData} {mapx} {mapy}/>
{/if}

<div class="sidebar">
    Longitude: {lng.toFixed(4)} | Latitude: {lat.toFixed(4)} | Zoom:{zoom.toFixed(2)}
</div>

<button onclick={handleReset} class="reset-button">Reset</button>
