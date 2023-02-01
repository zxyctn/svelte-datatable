<script>
  import { writable } from "svelte/store";

  import {
    createTable,
    Subscribe,
    Render,
    createRender,
  } from "svelte-headless-table";

  import {
    addSortBy,
    addPagination,
    addExpandedRows,
  } from "svelte-headless-table/plugins";

  import {
    DropdownItem,
    DropdownMenu,
    DropdownToggle,
    ButtonDropdown,
  } from "sveltestrap";

  import data from "../data";
  import filter from "../filter";
  import TableButton from "./helpers/TableButtons.svelte";
  import FilteringDropdown from "./FilteringDropdown.svelte";

  const types = {
    id: "numeric",
    name: "text",
    description: "text",
  };

  const filteredData = writable([]);

  const handleFiltering = (e) => {
    const { column, firstFilter, secondFilter } = e.detail;
    const firstFiltered = filter(firstFilter, column, data);
    const secondFiltered = filter(secondFilter, column, firstFiltered);
    filteredData.set(secondFiltered);
  };

  const table = createTable(filteredData, {
    sort: addSortBy({ disableMultiSort: true }),
    page: addPagination({ initialPageSize: 10, serverSide: true }),
    expand: addExpandedRows(),
  });

  const columns = table.createColumns([
    table.column({
      header: "ID",
      accessor: "id",
      plugins: {
        sort: { invert: true },
      },
    }),
    table.column({
      header: "Name",
      accessor: "name",
      plugins: {
        sort: { invert: true },
      },
    }),
    table.column({
      header: "Description",
      accessor: "description",
      plugins: {
        sort: { invert: true },
      },
    }),
    table.display({
      id: "options",
      header: "",
      cell: ({ row }, { pluginStates }) => {
        return createRender(TableButton, {
          row: row.cellForId,
        });
      },
    }),
  ]);

  const { headerRows, pageRows, tableAttrs, tableBodyAttrs, pluginStates } =
    table.createViewModel(columns);
  const { pageSize, pageIndex } = pluginStates.page;

  let hasNextPage = false;
  let hasPreviousPage = false;
  let rowCount = null;

  let isOpen = false;

  async function updateQuery() {
    const q = new URLSearchParams();

    q.set("limit", String($pageSize));
    q.set("offset", String($pageSize * $pageIndex));

    const fetchData = await fetch(`http://127.0.0.1:8000/groups/?${q}`);
    const response = await fetchData.json();
    const results = response.results;

    hasPreviousPage = response.previous ? true : false;
    hasNextPage = response.next ? true : false;
    rowCount = response.count;

    filteredData.update((_) =>
      results.map((result) => ({
        ...result,
        description: `Testing ${result.id}`,
      }))
    );
  }

  updateQuery();
</script>

<table {...$tableAttrs} class="table table-hover">
  <caption>Svelte Headless Table + Sveltestrap</caption>
  <thead>
    {#each $headerRows as headerRow (headerRow.id)}
      <Subscribe
        rowAttrs={headerRow.attrs()}
        let:rowAttrs
        rowProps={headerRow.props()}
        let:rowProps
      >
        <tr {...rowAttrs}>
          {#each headerRow.cells as cell (cell.id)}
            <Subscribe
              attrs={cell.attrs()}
              props={cell.props()}
              let:props
              let:attrs
            >
              <th scope="col" {...attrs}>
                <div class="d-flex justify-content-between align-items-center">
                  <div>
                    <span
                      on:click={props.sort.toggle}
                      on:keydown={props.sort.toggle}
                    >
                      {cell.render()}
                    </span>
                    {#if props.sort.order === "asc"}
                      ▾
                    {:else if props.sort.order === "desc"}
                      ▴
                    {/if}
                  </div>
                  {#if cell.isData()}
                    <FilteringDropdown
                      column={cell.id}
                      on:submit={handleFiltering}
                      type={types[cell.id]}
                    />
                  {/if}
                </div>
              </th>
            </Subscribe>
          {/each}
        </tr>
      </Subscribe>
    {/each}
  </thead>

  <tbody class="table-group-divider" {...$tableBodyAttrs}>
    {#each $pageRows as row (row.id)}
      <Subscribe rowAttrs={row.attrs()} let:rowAttrs>
        <tr {...rowAttrs}>
          {#each row.cells as cell (cell?.id)}
            <Subscribe attrs={cell.attrs()} let:attrs>
              <td {...attrs}>
                {#if cell.isData}
                  <Render of={cell.render()} />
                {/if}
              </td>
            </Subscribe>
          {/each}
        </tr>
      </Subscribe>
    {/each}
  </tbody>
</table>
<div class="d-flex justify-content-center gap-1">
  <button
    class="btn btn-sm btn-dark"
    on:click={() => {
      $pageIndex = 0;
      updateQuery();
    }}
    disabled={!$pageIndex}>{"<<"}</button
  >
  <button
    class="btn btn-sm btn-dark"
    on:click={() => {
      --$pageIndex;
      updateQuery();
    }}
    disabled={!hasPreviousPage}>{"<"}</button
  >
  <ButtonDropdown {isOpen} toggle={() => (isOpen = !isOpen)}>
    <DropdownToggle color="dark" caret>{$pageSize}</DropdownToggle>
    <DropdownMenu>
      <DropdownItem
        on:click={() => {
          $pageSize = 1;
          updateQuery();
        }}>1</DropdownItem
      >
      <DropdownItem
        on:click={() => {
          $pageSize = 5;
          updateQuery();
        }}>5</DropdownItem
      >
      <DropdownItem
        on:click={() => {
          $pageSize = 10;
          updateQuery();
        }}>10</DropdownItem
      >
      <DropdownItem
        on:click={() => {
          $pageSize = 15;
          updateQuery();
        }}>15</DropdownItem
      >
    </DropdownMenu>
  </ButtonDropdown>
  <button
    class="btn btn-sm btn-dark"
    on:click={() => {
      ++$pageIndex;
      updateQuery();
    }}
    disabled={!hasNextPage}>{">"}</button
  >
  <button
    class="btn btn-sm btn-dark"
    on:click={() => {
      $pageIndex = Math.ceil(rowCount / $pageSize) - 1;
      updateQuery();
    }}
    disabled={$pageIndex == Math.ceil(rowCount / $pageSize) - 1}>{">>"}</button
  >
</div>
