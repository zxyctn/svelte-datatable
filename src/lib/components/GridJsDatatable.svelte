<script>
  import Grid from "gridjs-svelte";

  const columns = ["ID", "Name"];

  // Local Django Server
  const server = {
    url: "http://127.0.0.1:8000/groups/",
    then: (data) => data.results.map((group) => [group.id, group.name]),
    total: (data) => data.count,
  };

  const pagination = {
    limit: 5,
    server: {
      url: (prev, page, limit) =>
        `${prev}${prev.includes("search") ? "&" : "?"}limit=${limit}&offset=${
          page * limit
        }`,
    },
  };

  const search = {
    server: {
      url: (prev, keyword) => `${prev}?search=${keyword}`,
    },
  };
</script>

<Grid {columns} {server} {pagination} sort {search} />

<style global>
  @import "https://cdn.jsdelivr.net/npm/gridjs/dist/theme/mermaid.min.css";
</style>
