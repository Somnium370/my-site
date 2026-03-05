<script lang="ts">
    type DailyForecast = {
        time: string[];
        temperature_2m_max: number[];
        temperature_2m_min: number[];
        precipitation_probability_max:number[];
    };
    

let { forecast } = $props<{
forecast: DailyForecast | null;
}>();
    </script>
{#if forecast}
<table class = "w-full text-left border-collapse mt-4">
    <thead>
        <tr class = "border-b">
            <th class = "py-2 font-semibold">Date</th>
            <th class = "py-2 font-semibold">High</th>
            <th class = "py-2 font-semibold">Low</th>
            <th class = "py-2 font-semibold">Rain %</th>
        </tr>
    </thead>
    <tbody>
        {#each forecast.time as day, i}
        <tr class="border-b">
            <td class="py-2">{day}</td>
            <td class="py-2">
                {forecast.temperature_2m_max[i]}°F
            </td>
            <td class="py-2">
                {forecast.temperature_2m_min[i]}°F
            </td>
            <td class="py-2">
                {forecast.precipitation_probability_max[i]}%
            </td>
        </tr>
        {/each}
    </tbody>
</table>
{/if}