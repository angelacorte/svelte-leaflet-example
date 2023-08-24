<script>
  import * as L from 'leaflet';
  // If you're playing with this in the Svelte REPL, import the CSS using the
  // syntax in svelte:head instead. For normal development, this is better.
  import 'leaflet/dist/leaflet.css';
  import Geolocation from "svelte-geolocation";
  let map;

  let getPosition = false;
  let coords = []; // pos 0 is longitude, pos 1 is latitude
  let uni = [44.14781236057191, 12.235733504482019];

  let locations = [[44.14781335057191, 12.235743504482019],
    [44.14781437057191, 12.236753504482019],
    [44.14781538057191, 12.237763504482019],
    [44.14781639057191, 12.238773504482019]]

  function createMap(container) {
    //setView() wants [latitude, longitude]
    let m = L.map(container).setView([coords[1], coords[0]], 17);
    L.tileLayer(
            'https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png',
            {
              attribution: `&copy;<a href="https://www.openstreetmap.org/copyright" target="_blank">OpenStreetMap</a>,
          &copy;<a href="https://carto.com/attributions" target="_blank">CARTO</a>`,
              subdomains: 'abcd',
              maxZoom: 23,
            }
    ).addTo(m);
    let marker = L.marker([coords[1], coords[0]]).addTo(m).bindPopup('you are here').openPopup();
    locations.forEach(loc => {
      let mark = L.marker(loc).addTo(m).bindPopup("Pos");
    })
    return m;
  }

  function mapAction(container) {
    map = createMap(container);
    return {
      destroy: () => {
        map.remove();
      },
    };
  }

  function resizeMap() {
    if(map) { map.invalidateSize(); }
  }
</script>

<svelte:window on:resize={resizeMap} />
<button on:click="{() => (getPosition = true)}"> Get actual location </button>

<Geolocation
        getPosition="{getPosition}"
        bind:coords
        let:loading
        let:success
        let:error
        let:notSupported>
  {#if notSupported}
    Your browser does not support the Geolocation API.
  {:else}
    {#if loading}
      Loading...
    {/if}
    {#if success}
      <div id="app" style="height:500px;width:500px" use:mapAction></div>

    {/if}
    {#if error}
      An error occurred. {error.code} {error.message}
    {/if}
  {/if}
</Geolocation>
