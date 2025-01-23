<script lang="ts">
    import { onMount } from "svelte";

    let rates: Record<string, number> = $state({});
    let fromCurrency = $state("PHP");
    let toCurrency = $state("USD");
    let amount = $state(50);
    let total = $state("");

    onMount(async () => {
        try {
            const response = await fetch(
                `https://api.freecurrencyapi.com/v1/latest?apikey=fca_live_2KJqrpWeUBBD3abxKDA32wP9qsbHX9Ojciz05mob`
            );
            const data = await response.json();
            rates = data.data;
        } catch (error) {
            console.error("Error fetching currency data:", error);
        }
    });

    $effect(() => {
        if (rates[fromCurrency] && rates[toCurrency]) {
        const fromRate = rates[fromCurrency];
        const toRate = rates[toCurrency];
        total = ((amount * toRate) / fromRate).toFixed(2);
        }
    });
</script>



<style>
    h1 {
        text-align: center;
    }
    .converter{
        display: flex;
        flex-direction: column;
        background-color: #f4f4f9;
        border-radius: 8px;
        box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
        width: 100%;
        max-width: 400px;
        padding: 20px;
    }

    .inputCenter{
        display: flex;
        justify-content: center;
    }

    input[type="number"]{
        width: 30%;
        justify-content: center;
    }

    .valueSelector{
        display: flex;
        justify-content: center;
        width: 100%;
    }

    h2{
        font-size: larger;
        text-align: center;
    }

   
</style>

<div class="converter">
    <h1>Currency Converter</h1>

    <div class="inputCenter">
        <label style="padding-right: 10px;" for="inputNumber">Amount: </label><br />
        
        <input id="inputNumber" type="number" bind:value={amount} placeholder="Amount" />
    </div>

    <br />

    <div class="valueSelector">
        <label style="padding-right: 10px;" for="from">From: </label>
        <select id="from" bind:value={fromCurrency}>
            {#each Object.keys(rates) as currency}
                <option value={currency}>{currency}</option>
            {/each}
        </select>
        
        <label style="padding-right: 10px; padding-left: 50px" for="to">To: </label>
        <select id="to" bind:value={toCurrency}>
            {#each Object.keys(rates) as currency}
                <option value={currency}>{currency}</option>
            {/each}
        </select>
    </div>
    <br />

    <h2>{amount} {fromCurrency} = {total || "..."} {toCurrency}</h2>
</div>
