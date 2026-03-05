<script lang="ts">
    import { Button } from "$lib/components/ui/button/index.js";
    let meals = $state<any[]>([]);
    let query = $state("");

    async function searchRecipe() {
        const res = await fetch(
            `https://www.themealdb.com/api/json/v1/1/search.php?s=${query}`
        );

        const data = await res.json();
        meals = data.meals ?? [];
    }
    function handleKey(e: KeyboardEvent) {
        if (e.key === "Enter") {
            searchRecipe();
        }
    }
</script>

<h1 class="text-3xl font-bold mb-4">
    Recipe for Today
</h1>

<div class="flex gap-2 mb-4">
    <input
        bind:value={query}
        placeholder="Search recipe..."
        class="border p-2 rounded flex-1"
        on:keydown={handleKey}
    />
    <Button onclick={searchRecipe}>Search</Button>
</div>

{#each meals as meal}
<div class="border p-4 mt-4 rounded">
    <h2 class="font-bold">{meal.strMeal}</h2>
    <img src={meal.strMealThumb} width="200"/>
</div>
{/each}