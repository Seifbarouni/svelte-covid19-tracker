<script>
  import Header from "./components/Header.svelte";
  import { onMount } from "svelte";
  import { fade } from "svelte/transition";

  let data = {};
  let countries = [];
  let selected;
  let ctr = "";
  let newCases = "";
  let totalCases = "";
  let newDeaths = "";
  let totalDeaths = "";
  let newRecovered = "";
  let totalRecovered = "";
  let formattedDate = "";
  let loading = false;

  onMount(async () => {
    loading = true;
    const res = await fetch(`https://api.covid19api.com/summary`);
    data = await res.json();
    global();
    loading = false;
  });
  function handleChange() {
    if (selected != "" && selected != ctr) {
      let newCtr = countries.find((c) => c.Country == selected);
      newCases = newCtr.NewConfirmed;
      totalCases = newCtr.TotalConfirmed;
      newDeaths = newCtr.NewDeaths;
      totalDeaths = newCtr.TotalDeaths;
      newRecovered = newCtr.NewRecovered;
      totalRecovered = newCtr.TotalRecovered;
      ctr = selected;
    }
  }
  function global() {
    ctr = "Global";
    newCases = data.Global.NewConfirmed;
    totalCases = data.Global.TotalConfirmed;
    newDeaths = data.Global.NewDeaths;
    totalDeaths = data.Global.TotalDeaths;
    newRecovered = data.Global.NewRecovered;
    totalRecovered = data.Global.TotalRecovered;
    countries = data.Countries;
    formattedDate = formatDate(data.Global.Date);
    selected = "";
  }
  function formatDate(dateStr) {
    let date = new Date(dateStr);
    let year = date.getFullYear();
    let month = date.getMonth() + 1;
    let dt = date.getDate();

    date.get;

    if (dt < 10) {
      dt = "0" + dt;
    }
    if (month < 10) {
      month = "0" + month;
    }

    return (
      year +
      "-" +
      month +
      "-" +
      dt +
      " " +
      date.getHours() +
      ":" +
      date.getMinutes()
    );
  }
</script>

<svelte:head>
  <title>Covid-19 Tracker | {ctr}</title>
</svelte:head>

{#if loading == true}
  <div class="min-h-screen flex justify-center items-center">
    <img src="img/loading.gif" alt="" height="20px" width="300px" />
  </div>
{:else}
  <Header />
  <div
    class="flex flex-col items-center justify-center mt-12"
    in:fade={{ duration: 500 }}
  >
    <div class="flex justify-center flex-col items-center">
      <div class="font-bold md:text-2xl text-xl">{ctr}</div>
      <div class="md:text-xl text-base mt-2">
        {formattedDate}
      </div>
    </div>
    <div class="flex lg:flex-row flex-col  justify-center mt-6">
      <div
        class="bg-indigo-300 p-3 md:w-96 w-72 h-44 rounded-md shadow-lg
    flex flex-col justify-evenly items-center hover:shadow-xl
    "
      >
        <div class="font-bold text-indigo-600 text-xl">Cases</div>
        <div><span class="font-bold">New: </span>{newCases}</div>
        <div><span class="font-bold">Total: </span>{totalCases}</div>
      </div>
      <div>&nbsp;</div>
      <div
        class="bg-indigo-300  p-3 md:w-96 w-72 h-44 rounded-md shadow-lg
    flex flex-col justify-evenly items-center hover:shadow-xl
    "
      >
        <div class="font-bold text-indigo-600 text-xl">Deaths</div>
        <div><span class="font-bold">New: </span>{newDeaths}</div>
        <div><span class="font-bold">Total: </span>{totalDeaths}</div>
      </div>
    </div>
    <div>&nbsp;</div>
    <div
      class="bg-indigo-300  p-3 md:w-96 w-72 h-44 rounded-md shadow-lg
  flex flex-col justify-evenly items-center hover:shadow-xl
  "
    >
      <div class="font-bold text-indigo-600 text-xl">Recovered</div>
      <div><span class="font-bold">New: </span>{newRecovered}</div>
      <div><span class="font-bold">Total: </span>{totalRecovered}</div>
    </div>
    <div class="mt-6 mb-2">
      <!-- svelte-ignore a11y-no-onchange -->
      <select
        class="focus:outline-none w-42 border border-indigo-200 md:text-base text-sm rounded-md"
        style="-webkit-appearance: none;"
        bind:value={selected}
        on:change={handleChange}
      >
        <option value="">Select Country</option>
        {#each countries as country}
          <option value={country.Country}>{country.Country}</option>
        {/each}
      </select>
    </div>
    {#if ctr != "Global"}
      <div class="mt-2  w-36  text-center  rounded-md text-indigo-600    mb-4 ">
        <button
          class="focus:outline-none md:text-base text-sm font-bold hover:text-indigo-400 "
          on:click={global}>Back to Global</button
        >
      </div>
    {/if}
  </div>
{/if}
