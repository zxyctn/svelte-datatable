<script>
  import { createEventDispatcher } from "svelte";
  import {
    Styles,
    Icon,
    DropdownItem,
    DropdownMenu,
    DropdownToggle,
    ButtonDropdown,
  } from "sveltestrap";

  import Filter from "./helpers/Filter.svelte";

  const dispatch = createEventDispatcher();

  function filter() {
    dispatch("submit", {
      column: column,
      type: type,
      firstFilter: {
        option: firstFilter,
        value: firstInput,
      },
      secondFilter: {
        option: secondFilter,
        value: secondInput,
      },
    });

    isOpen = false;
  }

  export let column;
  export let type;

  let isOpen = false;

  let firstFilter = "isequal";
  let firstInput = "";

  let secondFilter = "isequal";
  let secondInput = "";
</script>

<ButtonDropdown {isOpen} toggle={() => (isOpen = !isOpen)}>
  <DropdownToggle tag="div" class="d-inline-block">
    <Icon name="funnel-fill" />
  </DropdownToggle>

  <DropdownMenu>
    <DropdownItem header>Show rows with value that</DropdownItem>

    <Filter
      bind:filterOption={firstFilter}
      bind:filterValue={firstInput}
      {type}
    />

    <DropdownItem header>And</DropdownItem>

    <Filter
      bind:filterOption={secondFilter}
      bind:filterValue={secondInput}
      {type}
    />

    <button class="btn btn-primary mx-3 mt-1" on:click={filter}>Filter</button>
  </DropdownMenu>
</ButtonDropdown>

<Styles />
