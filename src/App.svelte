<script>
  import GridJsDatatable from "./lib/components/GridJsDatatable.svelte";
  import ShtDatatable from "./lib/components/SHTDatatable.svelte";
  import ShtDatatableServer from "./lib/components/SHTDatatableServer.svelte";

  let data = "static";
</script>

<div class="container my-5">
  <!-- <h1>Grid.js</h1>
  <GridJsDatatable />
  <hr /> -->

  <div class="d-flex justify-content-between">
    <h1>Manage Groups</h1>

    <div class="d-flex align-items-center gap-3">
      <label for="dataSelect">Data: </label>
      <select class="form-select" id="dataSelect" bind:value={data}>
        <option value="static">Static</option>
        <option value="server">Server-side</option>
      </select>
    </div>
  </div>

  {#if data === "static"}
    <ShtDatatable />
  {:else}
    <ShtDatatableServer />
  {/if}
</div>
