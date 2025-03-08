<template>
  <div id="app">
    <!-- Barra de Menu -->
    <div class="barra-menu">
      <div class="logo">SNAGH | NEWS</div>
      <div class="links">
        <a href="#noticias-brasil">Notícias do Brasil</a>
        <a href="#noticias-mundo">No Mundo</a>
      </div>
    </div>

    <!-- Conteúdo Principal -->
    <NoticiasPage />

    <!-- Botão Flutuante para Voltar ao Topo -->
    <button class="botao-topo" @click="voltarAoTopo">↑</button>
  </div>
</template>

<script>
import NoticiasPage from "./components/NoticiasPage.vue";

export default {
  name: "App",
  components: {
    NoticiasPage,
  },
  methods: {
    voltarAoTopo() {
      window.scrollTo({
        top: 0,
        behavior: "smooth",
      });
    },
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
  background-color: #f2f2f2;
}

#app {
  width: 100%;
  height: 100%;
  background-color: #f2f2f2;
}

/* Estilos da Barra de Menu */
.barra-menu {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 20px;
  background-color: #595959;
  border-bottom: 2px solid #f22259;
  position: sticky;
  top: 0;
  z-index: 1000;
}

.logo {
  font-family: "Arial Black", sans-serif;
  font-size: 1.2em;
  color: #ffffff;
  font-weight: bold;
  white-space: nowrap;
}

.links {
  display: flex;
  align-items: center;
  white-space: nowrap;
}

.links a {
  font-family: Arial, sans-serif;
  font-size: 0.9em;
  color: #ffffff;
  text-decoration: none;
  margin-left: 15px;
  transition: color 0.3s ease;
  text-transform: uppercase;
}

/* Ajustes para telas pequenas */
@media (max-width: 600px) {
  .logo {
    font-size: 1em;
  }

  .links a {
    font-size: 0.8em;
    margin-left: 10px;
  }
}

/* Estilos do Botão Flutuante */
.botao-topo {
  position: fixed;
  bottom: 20px;
  right: 20px;
  width: 3rem;
  height: 3rem;
  background-color: #f22259;
  color: white;
  border: none;
  border-radius: 10px;
  font-size: 1.5rem;
  cursor: pointer;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  transition: background-color 0.3s ease, transform 0.3s ease;
}

.botao-topo:hover {
  background-color: #3dc87e;
  transform: scale(1.1);
}
</style>
