<script>
    import { onMount, onDestroy } from 'svelte';
    import mapboxgl from "mapbox-gl"
    import 'mapbox-gl/dist/mapbox-gl.css';

    let map;
    let mapContainer;
    let lng, lat, zoom;

    //London
    lng = -0.1276;
    lat = 51.5072;
    zoom = 11;

    let markers = [];
    let locations = [];

    let initialState = { lng, lat, zoom };

    const MAPBOX_ACCESS_TOKEN = "pk.eyJ1Ijoib2Jpd3VqaSIsImEiOiJja3B3ZHdvenkwMHV4MnFucHc2YW9tcHcyIn0.wI9Kn7vACQdq61dnjStghg"

    // Define colors for each store type
  const storeColors = {
    "McDonald's": "#FF0000", // Red
    "Morleys": "#FF4500", // Orange-Red
    "Poundland": "#008000", // Green
    "Waitrose": "#32CD32", // Lime Green
    "Lidl": "#0000FF", // Blue
    "Pret A Manger": "#800080" // Purple
  };


    async function loadData() {
    const response = await fetch("/london_food_desert_stores.json");
    locations = await response.json();
    console.log(locations);
    loadmarks()
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
        zoom: initialState.zoom
    });

    map.on('move', () => {
        updateData();
    })

    onDestroy(() => {
    map.remove();
  })

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


</style>

<div class="map" bind:this={mapContainer}></div>

<div class="sidebar">
    Longitude: {lng.toFixed(4)} | Latitude: {lat.toFixed(4)} | Zoom:{zoom.toFixed(2)}
</div>

<button onclick={handleReset} class="reset-button">Reset</button>
