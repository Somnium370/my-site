<script lang="ts">
    import { Button } from "$lib/components/ui/button/index.js";
    import { Input } from "$lib/components/ui/input/index.js";
    import * as Card from "$lib/components/ui/card/index.js";
    import WeatherMap from "$lib/components/ui/WeatherMap.svelte";
    import ForecastTable from "$lib/components/ui/ForcastTable.svelte";
    
    
    // NEW type — describes the 7-day forecast
    type DailyForecast = {
        time: string[];
        temperature_2m_max: number[];
        temperature_2m_min: number[];
        precipitation_probability_max:
        number[];
    };
    type WeatherData = {
        current: {
            temperature_2m: number;
            weathercode: number;
            windspeed_10m: number;
        };
    };

    let city = $state<string>('Chicago');
    let errorMsg = $state("");
    let forecast = $state<DailyForecast | null>(null);
    let mapLon = $state(-87.63); // Chicago longitude
    let mapLat = $state(41.88); // Chicago latitude
    let searchedCity = $state("Chicago");
    let avgHigh = $derived(// Average high temp across 7-day forecast
        forecast
        ? Math.round(
            forecast.temperature_2m_max
            .reduce((sum, t) => sum + t, 0)
            / forecast.temperature_2m_max.length
        )
        : null
    );
    $effect(() => {// Save the last saved city to browser
        if (searchedCity) {
            localStorage.setItem("lastCity", searchedCity);
        }
    });
    $effect(()=>{
        const saved = localStorage.getItem("lastCity");
        if (saved) {
            city = saved;
        }
    });
    let weather = $state<WeatherData | null>(null); // null = no data yet
    let loading = $state<boolean>(false);
    let error = $state(null);

    // City data - kys are names, values and coordinates 
    const cities ={
        Chicago: {lat: 41.88, lon: -87.63},
        'New York': {lat: 40.71, lon:-74.01},
        London: {lat: 51.51, lon:-0.13},
        Tokyo: {lat: 35.68, lon:139.69},
        Sydney: {lat: -33.87, lon:151.21},
    }

    // WEATHER CODE LOOKUP — API returns numbers, we want words
    const weatherDescriptions = {
    0: 'Clear sky', 1: 'Mostly clear',
    2: 'Partly cloudy', 3: 'Overcast',
    45: 'Foggy', 51: 'Drizzle', 61: 'Rain',
    71: 'Snow', 80: 'Showers', 95: 'Thunderstorm',
    };

    // The fetch pattern - this is the core skill today
    /*async function fetchWeather(){
        loading = true;
        error = null;

        try {
            const {lat, lon} = cities[city];
            const url = `https://api.open-meteo.com/v1/forecast` + 
            `?latitude=${lat}&longitude=${lon}` +
            `&current=temperature_2m,weathercode,windspeed_10m` +
            `&temperature_unit=fahrenheit&windspeed_unit=mph`;
            const response = await fetch(url);
            weather = await response.json();
        } catch (e){
            error = 'Failed to fetch weather data';
        }
        loading = false;
    }*/
    async function searchCity() {
    // Convert city name to coordinates
    const geoUrl =`https://geocoding-api.open-meteo.com/v1/search?name=${city}&count=1`;
    const geoRes = await fetch(geoUrl);
    const geoData = await geoRes.json();

    if (!geoData.results) { errorMsg = "City not found"; return; }
    const { latitude, longitude, name } = geoData.results[0];
    errorMsg = "";
    mapLon = longitude;
    mapLat = latitude;
    searchedCity = name;
    // fetch 7-day forecast
    const weatherUrl = `https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude`+
    `=${longitude}&daily=temperature_2m_max,temperature_2m_min,precipitation_probability_max`+
    `&current=temperature_2m&temperature_unit=fahrenheit&timezone=auto`;
    const res = await fetch(weatherUrl);
    const data = await res.json();
    weather = data;
    forecast = data.daily;
    }
</script>
<h1 class='text-3xl font-bold mb-4'>
    Weather
</h1>
<div class = "flex gap-3 mb_6">
    <select bind:value={city} class="p-2 border rounded">
        {#each Object.keys(cities) as c}
        <option>{c}</option>
    {/each}
</select>
<Button onclick={searchCity}>
    Get Weather
</Button>
</div>
{#if loading} <p class="text-gray-500">Loading...</p> {/if}
{#if error} <p class="text-red-600 font-bold">{error}</p> {/if}
{#if weather}
<div class="flex gap-2 mb-4">
<Input placeholder="Enter a city..."
bind:value={city} />
<Button onclick={searchCity}>Search</Button>
</div>
<ForecastTable forecast={forecast} />
{#if avgHigh !== null}
<p class = "text-lg font-semibold">
    7-day ave high: {avgHigh}°F
</p>
{/if}
<WeatherMap lon = {mapLon} lat={mapLat} cityName={searchedCity} />
    <Card.Root>
        <Card.Header>
            <Card.Title>{city}</Card.Title>
            <Card.Description>Current conditions</Card.Description>
        </Card.Header>
        <Card.Content>
            <p class= "text-4xl font-bold">
                {weather.current.temperature_2m} F
            </p>
                </Card.Content>
        </Card.Root>
{/if}


