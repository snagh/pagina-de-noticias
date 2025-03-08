<template>
  <div class="noticias">
    <!-- Seção: Últimas Notícias do Brasil -->
    <h1 id="noticias-brasil" class="titulo-secao">Notícias do Brasil</h1>
    <div v-if="carregando">Carregando notícias...</div>
    <div v-else-if="erro" class="erro">{{ erro }}</div>
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
    <h1 id="noticias-mundo" class="titulo-secao">No Mundo</h1>
    <div v-if="carregando">Carregando notícias...</div>
    <div v-else-if="erro" class="erro">{{ erro }}</div>
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
  background-color: #f2f2f2;
  color: #2c3e50;
  min-height: 100vh;
  box-sizing: border-box;
  width: 100%;
}

.titulo-secao {
  font-family: "Arial Black", sans-serif;
  font-size: 2.5em;
  text-align: center;
  color: #f22259;
  margin: 40px 0 20px;
  white-space: nowrap; /* Impede a quebra de linha por padrão */
  overflow: hidden; /* Esconde o texto que ultrapassar */
  text-overflow: ellipsis; /* Adiciona "..." se o texto for muito longo */
  width: 100%;
  padding: 0 20px;
  box-sizing: border-box;
}

.grid-noticias {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 20px;
  padding: 20px;
  margin: 0;
}

.bloco-noticia {
  background-color: #eaeaea;
  border-radius: 10px;
  padding: 15px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  overflow: hidden;
  text-decoration: none;
  color: inherit;
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
  max-height: 150px;
  object-fit: cover;
  border-radius: 8px;
  margin-bottom: 10px;
}

.bloco-noticia p {
  color: #2c3e50;
  font-size: 0.9em;
  flex-grow: 1;
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 3;
  line-clamp: 3;
  -webkit-box-orient: vertical;
  margin-bottom: 10px;
}

.bloco-noticia span {
  color: #f25c84;
  font-weight: bold;
  text-align: center;
  display: block;
  margin-top: auto;
}

.paginacao {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
  padding-bottom: 80px; /* Espaço extra para o botão flutuante */
}

button {
  background-color: #3dc87e;
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

.erro {
  color: red;
  text-align: center;
  margin-top: 20px;
}

/* Responsividade */
@media (max-width: 1200px) {
  .grid-noticias {
    grid-template-columns: repeat(3, 1fr);
  }

  .titulo-secao {
    width: 30%;
  }
}

@media (max-width: 900px) {
  .grid-noticias {
    grid-template-columns: repeat(2, 1fr);
  }

  .titulo-secao {
    width: 50%;
  }
}

@media (max-width: 600px) {
  .grid-noticias {
    grid-template-columns: 1fr;
  }

  .titulo-secao {
    width: 80%;
    font-size: 2em;
    white-space: normal; /* Permite a quebra de linha em celulares */
    text-overflow: clip; /* Remove o "..." em celulares */
  }
}
</style>
