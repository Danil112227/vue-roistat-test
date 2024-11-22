<template>
  <div id="app">
    <UserTable />
  </div>
</template>

<script>
import UserTable from "./components/UserTable.vue";

export default {
  name: "App",
  components: {
    UserTable,
  },
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
}

#app {
  padding: 20px;
}
</style>
