<template>
  <div id="app">
    <!-- Barra de Menu -->
    <div class="barra-menu">
      <a href="/" class="logo" @click.prevent="recarregarPagina">
        SNAGH | NEWS
      </a>
      <div class="separador"></div>
      <div class="links">
        <a href="#noticias-brasil" @click.prevent="mostrarSubBarra('brasil')"
          >NOTÍCIAS DO BRASIL</a
        >
        <a href="#noticias-mundo" @click.prevent="mostrarSubBarra('mundo')"
          >NO MUNDO</a
        >
      </div>
    </div>

    <!-- Sub-Barra de Tópicos -->
    <div v-if="subBarraVisivel" class="sub-barra">
      <a
        v-for="topico in topicos"
        :key="topico.id"
        href="#"
        @click.prevent="filtrarPorTopico(topico.id)"
      >
        {{ topico.nome }}
      </a>
    </div>

    <!-- Conteúdo Principal -->
    <NoticiasPage />

    <!-- Botão Flutuante para Voltar ao Topo -->
    <button v-if="mostrarBotao" class="botao-topo" @click="voltarAoTopo">
      ↑
    </button>
  </div>
</template>

<script>
import NoticiasPage from "./components/NoticiasPage.vue";
import { eventBus } from "./eventBus"; // Importe o eventBus

export default {
  name: "App",
  components: {
    NoticiasPage,
  },
  data() {
    return {
      mostrarBotao: false,
      subBarraVisivel: false,
      secaoAtual: null,
      topicos: [
        { id: "sport", nome: "Esportes" },
        { id: "education", nome: "Educação" },
        { id: "business", nome: "Economia" },
        { id: "environment", nome: "Ambiente" },
      ],
    };
  },
  methods: {
    voltarAoTopo() {
      window.scrollTo({
        top: 0,
        behavior: "smooth",
      });
    },
    verificarScroll() {
      if (window.scrollY > 100) {
        this.mostrarBotao = true;
      } else {
        this.mostrarBotao = false;
      }
    },
    recarregarPagina() {
      window.location.reload();
    },
    mostrarSubBarra(secao) {
      this.subBarraVisivel = true;
      this.secaoAtual = secao;
    },
    filtrarPorTopico(topico) {
      // Emite o evento usando o eventBus
      eventBus.emit("filtrar-noticias", {
        secao: this.secaoAtual,
        topico: topico,
      });
      this.subBarraVisivel = false;
    },
  },
  mounted() {
    window.addEventListener("scroll", this.verificarScroll);
  },
  beforeUnmount() {
    window.removeEventListener("scroll", this.verificarScroll);
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
  justify-content: flex-start;
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
  text-decoration: none;
  margin-right: 10px;
}

.logo:hover {
  color: #3dc87e;
}

.separador {
  width: 2px;
  height: 30px;
  background-color: #444;
  margin-right: 15px;
}

.links {
  display: flex;
  align-items: center;
  white-space: nowrap;
}

.links a {
  font-family: "Arial Black", sans-serif;
  font-size: 0.9em;
  color: #ffffff;
  text-decoration: none;
  margin-left: 15px;
  transition: color 0.3s ease;
  text-transform: uppercase;
}

.links a:hover {
  color: #3dc87e;
}

/* Responsividade para celulares */
@media (max-width: 600px) {
  .logo {
    font-size: 1em;
  }

  .links a {
    font-size: 0.8em;
    margin-left: 10px;
    white-space: normal;
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

.sub-barra {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 10px;
  background-color: #444;
  border-bottom: 2px solid #f22259;
}

.sub-barra a {
  font-family: "Arial Black", sans-serif;
  font-size: 0.9em;
  color: #ffffff;
  text-decoration: none;
  margin: 0 10px;
  transition: color 0.3s ease;
  text-transform: uppercase;
}

.sub-barra a:hover {
  color: #3dc87e;
}
</style>
