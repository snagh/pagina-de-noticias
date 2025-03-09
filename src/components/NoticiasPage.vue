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
          :key="noticia.id"
          :href="noticia.webUrl"
          target="_blank"
          class="bloco-noticia"
        >
          <h2>{{ noticia.webTitle }}</h2>
          <img
            v-if="noticia.fields && noticia.fields.thumbnail"
            :src="noticia.fields.thumbnail"
            :alt="noticia.webTitle"
          />
          <p v-if="noticia.fields && noticia.fields.trailText">
            {{ noticia.fields.trailText }}
          </p>
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
          :key="noticia.id"
          :href="noticia.webUrl"
          target="_blank"
          class="bloco-noticia"
        >
          <h2>{{ noticia.webTitle }}</h2>
          <img
            v-if="noticia.fields && noticia.fields.thumbnail"
            :src="noticia.fields.thumbnail"
            :alt="noticia.webTitle"
          />
          <p v-if="noticia.fields && noticia.fields.trailText">
            {{ noticia.fields.trailText }}
          </p>
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
import { eventBus } from "../eventBus"; // Importe o eventBus

export default {
  name: "NoticiasPage",
  data() {
    return {
      noticiasBrasil: [],
      noticiasMundo: [],
      carregando: true,
      erro: null,
      paginaAtualBrasil: 1,
      paginaAtualMundo: 1,
      noticiasPorPagina: 8,
      topicoAtual: null,
    };
  },
  computed: {
    noticiasBrasilPaginaAtual() {
      const start = (this.paginaAtualBrasil - 1) * this.noticiasPorPagina;
      const end = start + this.noticiasPorPagina;
      return this.noticiasBrasil.slice(start, end);
    },
    noticiasMundoPaginaAtual() {
      const start = (this.paginaAtualMundo - 1) * this.noticiasPorPagina;
      const end = start + this.noticiasPorPagina;
      return this.noticiasMundo.slice(start, end);
    },
    totalPaginasBrasil() {
      return Math.ceil(this.noticiasBrasil.length / this.noticiasPorPagina);
    },
    totalPaginasMundo() {
      return Math.ceil(this.noticiasMundo.length / this.noticiasPorPagina);
    },
  },
  created() {
    this.carregarNoticias();
    // Escute o evento "filtrar-noticias" usando o eventBus
    eventBus.on("filtrar-noticias", (topico) => {
      this.filtrarNoticias(topico);
    });
  },
  methods: {
    async carregarNoticias() {
      try {
        const apiKey = process.env.VUE_APP_GUARDIAN_API_KEY;
        if (!apiKey) {
          throw new Error(
            "Chave da API do The Guardian não encontrada. Verifique o arquivo .env."
          );
        }

        const urlBrasil = `https://content.guardianapis.com/search?q=brazil&api-key=${apiKey}&show-fields=thumbnail,trailText`;
        const urlMundo = `https://content.guardianapis.com/search?api-key=${apiKey}&show-fields=thumbnail,trailText`;

        const [responseBrasil, responseMundo] = await Promise.all([
          axios.get(urlBrasil),
          axios.get(urlMundo),
        ]);

        if (responseBrasil.data && responseBrasil.data.response.results) {
          this.noticiasBrasil = responseBrasil.data.response.results;
        } else {
          throw new Error("Resposta da API do Brasil inválida.");
        }

        if (responseMundo.data && responseMundo.data.response.results) {
          this.noticiasMundo = responseMundo.data.response.results;
        } else {
          throw new Error("Resposta da API do Mundo inválida.");
        }
      } catch (error) {
        this.erro = `Erro ao carregar notícias: ${error.message}`;
        console.error("Erro na requisição:", error);
      } finally {
        this.carregando = false;
      }
    },
    filtrarNoticias({ secao, topico }) {
      this.topicoAtual = topico;
      this.carregarNoticiasPorTopico(secao, topico);
    },
    async carregarNoticiasPorTopico(secao, topico) {
      try {
        const apiKey = process.env.VUE_APP_GUARDIAN_API_KEY;
        const url = `https://content.guardianapis.com/search?q=${
          secao === "brasil" ? "brazil" : ""
        }&section=${topico}&api-key=${apiKey}&show-fields=thumbnail,trailText`;

        const response = await axios.get(url);

        if (secao === "brasil") {
          this.noticiasBrasil = response.data.response.results;
        } else {
          this.noticiasMundo = response.data.response.results;
        }
      } catch (error) {
        this.erro = `Erro ao carregar notícias: ${error.message}`;
        console.error("Erro na requisição:", error);
      }
    },
    paginaAnteriorBrasil() {
      if (this.paginaAtualBrasil > 1) this.paginaAtualBrasil--;
    },
    proximaPaginaBrasil() {
      if (this.paginaAtualBrasil < this.totalPaginasBrasil)
        this.paginaAtualBrasil++;
    },
    paginaAnteriorMundo() {
      if (this.paginaAtualMundo > 1) this.paginaAtualMundo--;
    },
    proximaPaginaMundo() {
      if (this.paginaAtualMundo < this.totalPaginasMundo)
        this.paginaAtualMundo++;
    },
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
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
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
  padding-bottom: 80px;
}

.paginacao button {
  background-color: #3dc87e;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
  margin: 0 10px;
}

.paginacao button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.erro {
  color: red;
  text-align: center;
  margin-top: 20px;
}

@media (max-width: 1000px) {
  .grid-noticias {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 600px) {
  .grid-noticias {
    grid-template-columns: 1fr;
  }
}
</style>
