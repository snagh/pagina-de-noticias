<template>
  <div id="app">
    <NoticiasPage />
  </div>
</template>

<script>
import NoticiasPage from "./components/NoticiasPage.vue";

export default {
  name: "App",
  components: {
    NoticiasPage,
  },
};
</script>

<style>
/* Estilos globais */
html,
body {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
  background-color: #062a02; /* Fundo verde para cobrir espaços em branco */
}

#app {
  width: 100%;
  height: 100%;
  background-color: #062a02; /* Fundo verde para cobrir espaços em branco */
}
</style>
