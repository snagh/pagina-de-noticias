<template>
  <div id="app">
    <NoticiasPage />
  </div>
</template>

<script>
import NoticiasPage from "./components/Noticias.vue";

export default {
  components: {
    NoticiasPage,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
