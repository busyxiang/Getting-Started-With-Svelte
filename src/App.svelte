<script>
  import { onMount } from "svelte";
  import ConversionGroup from "./ConversionGroup.svelte";

  const apiPrefix = "https://free.currconv.com/api/v7/";
  const apiKey = "e26cc7495b18320a13c8";
  const getAllCurrenciesAPI = `${apiPrefix}currencies?apiKey=${apiKey}`;
  const queryAPI = `${apiPrefix}{query}&compact=ultra&apiKey=${apiKey}`;

  let isLoading = true;
  let currencies = [];
  let convertData = { amount: 0, fromID: "MYR", toID: "USD" };
  let conversionRate = { from: 0, to: 0 };
  let convertResult = {
    amount: 0,
    convertedAmount: 0,
    fromID: "MYR",
    toID: "USD",
  };

  onMount(async () => {
    const res = await fetch(getAllCurrenciesAPI);
    const data = await res.json();

    currencies = Object.values(data.results);

    await updateConverionRate();

    isLoading = false;
  });

  function validateAmount() {
    convertData.amount = Math.max(0, convertData.amount);
  }

  function switchCurrency() {
    let temp = convertData.fromID;
    convertData.fromID = convertData.toID;
    convertData.toID = temp;

    updateConverionRate();
  }

  function calculateConversion() {
    Object.assign(convertResult, convertData);

    convertResult.convertedAmount = convertData.amount * conversionRate.to;
  }

  async function updateConverionRate() {
    isLoading = true;

    let api = queryAPI.replace(
      "{query}",
      `convert?q=${convertData.fromID}_${convertData.toID},${convertData.toID}_${convertData.fromID}`
    );

    const res = await fetch(api);
    const data = await res.json();

    conversionRate.to = data[`${convertData.fromID}_${convertData.toID}`];
    conversionRate.from = data[`${convertData.toID}_${convertData.fromID}`];

    isLoading = false;
  }
</script>

<main>
  <h1>Curreny Converter</h1>
  {#if isLoading}
    <div style="text-align: center;"><span class="spinner primary" /></div>
  {:else}
    <div class="container">
      <div class="card fluid">
        <div class="row">
          <div class="input-group vertical col-sm-3">
            <label for="Amount">Amount</label>
            <input
              bind:value={convertData.amount}
              type="number"
              placeholder="Amount to Convert"
              step=".01"
              on:change={validateAmount}
            />
          </div>
          <div class="input-group vertical col-sm-3">
            <label for="From">From</label>
            <select
              bind:value={convertData.fromID}
              on:change={updateConverionRate}
            >
              {#each currencies as currency}
                <option value={currency.id}>{currency.currencyName}</option>
              {/each}
            </select>
          </div>
          <div class="input-group vertical">
            <label for="Switch">Switch</label>
            <button on:click={switchCurrency}
              ><i class="fas fa-exchange-alt" /></button
            >
          </div>
          <div class="input-group vertical col-sm-3">
            <label for="To">To</label>
            <select
              bind:value={convertData.toID}
              on:change={updateConverionRate}
            >
              {#each currencies as currency}
                <option value={currency.id}>{currency.currencyName}</option>
              {/each}
            </select>
          </div>
          <div class="input-group vertical">
            <label for="Convert">Convert</label>
            <button on:click={calculateConversion}
              ><i class="fas fa-arrow-right" /></button
            >
          </div>
        </div>
      </div>
      <ConversionGroup {conversionRate} {convertResult} {convertData} />
    </div>
  {/if}
</main>

<style>
  h1,
  label {
    text-align: center;
  }

  .card {
    padding: 1rem;
  }
</style>
