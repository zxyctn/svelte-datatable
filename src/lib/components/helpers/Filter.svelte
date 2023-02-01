<script>
  export let filterOption = "isequal";
  export let filterValue = "";
  export let type = "text";

  const options = {
    numeric: [
      {
        value: "isequal",
        label: "Is equal to",
      },
      {
        value: "isgreaterorequal",
        label: "Is greater than or equal to",
      },
      {
        value: "isgreater",
        label: "Is greater than",
      },
      {
        value: "islessorequal",
        label: "Is less than or equal to",
      },
      {
        value: "isless",
        label: "Is less than",
      },
      {
        value: "isnotequal",
        label: "Is not equal to",
      },
    ],
    text: [
      {
        value: "isequal",
        label: "Is equal to",
      },
      {
        value: "isnotequal",
        label: "Is not equal to",
      },
      {
        value: "starts",
        label: "Starts with",
      },
      {
        value: "contains",
        label: "Contains",
      },
      {
        value: "notcontains",
        label: "Does not contain",
      },
      {
        value: "ends",
        label: "Ends with",
      },
    ],
  };
</script>

<div class="mx-3">
  <select
    class="form-select"
    aria-label="Show rows with value that"
    bind:value={filterOption}
  >
    {#each options[type] as option (option)}
      <option value={option.value}>{option.label}</option>
    {/each}
  </select>
  <input type="text" class="form-control mt-1" bind:value={filterValue} />
</div>
