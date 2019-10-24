<template>
  <div id="app">
    <vue-tree></vue-tree>
  </div>
</template>

<script>
import VueTree from "./components/VueTree.vue";

export default {
  name: "app",
  components: {
    VueTree
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
