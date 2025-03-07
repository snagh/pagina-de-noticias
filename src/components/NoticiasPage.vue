<template>
  <div class="noticias">
    <!-- Seção: Últimas Notícias do Brasil -->
    <h1 class="titulo-secao">Últimas Notícias do Brasil</h1>
    <div v-if="carregando">Carregando notícias...</div>
    <div v-else>
      <div class="grid-noticias">
        <a
          v-for="noticia in noticiasBrasilPaginaAtual"
          :key="noticia.url"
          :href="noticia.url"
          target="_blank"
          class="bloco-noticia"
        >
          <h2>{{ noticia.title }}</h2>
          <img v-if="noticia.image" :src="noticia.image" :alt="noticia.title" />
          <p>{{ noticia.description }}</p>
          <span>Leia mais</span>
        </a>
      </div>
      <div class="paginacao">
        <button
          @click="paginaAnteriorBrasil"
          :disabled="paginaAtualBrasil === 1"
        >
          Anterior
        </button>
        <span>Página {{ paginaAtualBrasil }} de {{ totalPaginasBrasil }}</span>
        <button
          @click="proximaPaginaBrasil"
          :disabled="paginaAtualBrasil === totalPaginasBrasil"
        >
          Próxima
        </button>
      </div>
    </div>

    <!-- Seção: Resto do Mundo -->
    <h1 class="titulo-secao">Resto do Mundo</h1>
    <div v-if="carregando">Carregando notícias...</div>
    <div v-else>
      <div class="grid-noticias">
        <a
          v-for="noticia in noticiasMundoPaginaAtual"
          :key="noticia.url"
          :href="noticia.url"
          target="_blank"
          class="bloco-noticia"
        >
          <h2>{{ noticia.title }}</h2>
          <img v-if="noticia.image" :src="noticia.image" :alt="noticia.title" />
          <p>{{ noticia.description }}</p>
          <span>Leia mais</span>
        </a>
      </div>
      <div class="paginacao">
        <button @click="paginaAnteriorMundo" :disabled="paginaAtualMundo === 1">
          Anterior
        </button>
        <span>Página {{ paginaAtualMundo }} de {{ totalPaginasMundo }}</span>
        <button
          @click="proximaPaginaMundo"
          :disabled="paginaAtualMundo === totalPaginasMundo"
        >
          Próxima
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "NoticiasPage",
  data() {
    return {
      noticiasBrasil: [], // Notícias do Brasil
      noticiasMundo: [], // Notícias do resto do mundo
      carregando: true,
      erro: null,
      paginaAtualBrasil: 1, // Paginação para notícias do Brasil
      paginaAtualMundo: 1, // Paginação para notícias do mundo
      noticiasPorPagina: 8, // 8 notícias por página (4 colunas x 2 linhas)
    };
  },
  computed: {
    // Notícias do Brasil na página atual
    noticiasBrasilPaginaAtual() {
      const inicio = (this.paginaAtualBrasil - 1) * this.noticiasPorPagina;
      const fim = inicio + this.noticiasPorPagina;
      return this.noticiasBrasil.slice(inicio, fim);
    },
    // Total de páginas para notícias do Brasil
    totalPaginasBrasil() {
      return Math.ceil(this.noticiasBrasil.length / this.noticiasPorPagina);
    },
    // Notícias do mundo na página atual
    noticiasMundoPaginaAtual() {
      const inicio = (this.paginaAtualMundo - 1) * this.noticiasPorPagina;
      const fim = inicio + this.noticiasPorPagina;
      return this.noticiasMundo.slice(inicio, fim);
    },
    // Total de páginas para notícias do mundo
    totalPaginasMundo() {
      return Math.ceil(this.noticiasMundo.length / this.noticiasPorPagina);
    },
  },
  methods: {
    // Paginação para notícias do Brasil
    paginaAnteriorBrasil() {
      if (this.paginaAtualBrasil > 1) {
        this.paginaAtualBrasil--;
      }
    },
    proximaPaginaBrasil() {
      if (this.paginaAtualBrasil < this.totalPaginasBrasil) {
        this.paginaAtualBrasil++;
      }
    },
    // Paginação para notícias do mundo
    paginaAnteriorMundo() {
      if (this.paginaAtualMundo > 1) {
        this.paginaAtualMundo--;
      }
    },
    proximaPaginaMundo() {
      if (this.paginaAtualMundo < this.totalPaginasMundo) {
        this.paginaAtualMundo++;
      }
    },
  },
  async created() {
    try {
      const apiKey = process.env.VUE_APP_NEWS_API_KEY;
      const urlBrasil = `https://gnews.io/api/v4/top-headlines?country=br&token=${apiKey}`;
      const urlMundo = `https://gnews.io/api/v4/top-headlines?token=${apiKey}`;

      // Busca notícias do Brasil
      const responseBrasil = await axios.get(urlBrasil);
      this.noticiasBrasil = responseBrasil.data.articles;

      // Busca notícias do mundo
      const responseMundo = await axios.get(urlMundo);
      this.noticiasMundo = responseMundo.data.articles;
    } catch (error) {
      this.erro = "Erro ao carregar notícias. Tente novamente mais tarde.";
      console.error("Erro na requisição:", error);
    } finally {
      this.carregando = false;
    }
  },
};
</script>

<style scoped>
.noticias {
  font-family: Arial, sans-serif;
  padding: 20px;
  background-color: #062a02; /* Verde escuro */
  color: #e0f0e0; /* Cor do texto para contrastar com o fundo */
  min-height: 100vh; /* Ocupa pelo menos 100% da altura da tela */
  box-sizing: border-box; /* Garante que o padding não aumente o tamanho total */
  width: 100%; /* Garante que o contêiner ocupe toda a largura */
}

.titulo-secao {
  font-family: "Arial Black", sans-serif;
  font-size: 2.5em;
  text-align: center;
  color: #42b983; /* Verde claro para o título */
  margin: 40px 0 20px;
  width: 20%; /* Ocupa 1/5 da tela */
  margin-left: auto;
  margin-right: auto;
}

.grid-noticias {
  display: grid;
  grid-template-columns: repeat(4, 1fr); /* Quatro colunas por padrão */
  gap: 20px; /* Espaçamento entre os blocos */
  padding: 20px;
  margin: 0; /* Remove margens padrão */
}

.bloco-noticia {
  background-color: #e0f0e0; /* Cor dos blocos */
  border-radius: 10px;
  padding: 15px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  overflow: hidden; /* Garante que o conteúdo não ultrapasse o bloco */
  text-decoration: none; /* Remove sublinhado do link */
  color: inherit; /* Herda a cor do texto do bloco */
}

.bloco-noticia:hover {
  transform: translateY(-5px);
  transition: transform 0.3s ease;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

.bloco-noticia h2 {
  color: #2c3e50;
  margin-bottom: 10px;
  font-size: 1.2em;
}

.bloco-noticia img {
  width: 100%;
  max-height: 150px; /* Altura máxima para a imagem */
  object-fit: cover; /* Garante que a imagem cubra o espaço sem distorcer */
  border-radius: 8px;
  margin-bottom: 10px;
}

.bloco-noticia p {
  color: #2c3e50;
  font-size: 0.9em;
  flex-grow: 1; /* Faz o parágrafo ocupar o espaço restante */
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 3; /* Limita o texto a 3 linhas (WebKit) */
  line-clamp: 3; /* Limita o texto a 3 linhas (padrão CSS) */
  -webkit-box-orient: vertical;
  margin-bottom: 10px; /* Espaço entre o parágrafo e o "Leia mais" */
}

.bloco-noticia span {
  color: #42b983; /* Cor do texto "Leia mais" */
  font-weight: bold;
  text-align: center;
  display: block; /* Faz o "Leia mais" ocupar uma linha própria */
  margin-top: auto; /* Garante que o "Leia mais" fique no final */
}

.paginacao {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
}

button {
  background-color: #42b983;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
  margin: 0 10px;
}

button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

/* Responsividade */
@media (max-width: 1200px) {
  .grid-noticias {
    grid-template-columns: repeat(3, 1fr); /* Três colunas para telas médias */
  }

  .titulo-secao {
    width: 30%; /* Ajusta a largura do título */
  }
}

@media (max-width: 900px) {
  .grid-noticias {
    grid-template-columns: repeat(2, 1fr); /* Duas colunas para tablets */
  }

  .titulo-secao {
    width: 50%; /* Ajusta a largura do título */
  }
}

@media (max-width: 600px) {
  .grid-noticias {
    grid-template-columns: 1fr; /* Uma coluna para celulares */
  }

  .titulo-secao {
    width: 80%; /* Aumenta a largura do título em telas pequenas */
    font-size: 2em;
  }
}
</style>
