<script lang="ts">
    import { MapLibre, DefaultMarker } from "svelte-maplibre";

let {lon, lat, cityName} = $props<{
    lon: number;
    lat: number;
    cityName: string;
}>();
</script>

<MapLibre center={[lon,lat]} zoom={10}
class="h-[400px] w full rounded-lg mt-4"
style="https://basemaps.cartocdn.com/gl/voyager-gl-style/style.json">
<DefaultMarker lngLat={[lon, lat]} />
</MapLibre>