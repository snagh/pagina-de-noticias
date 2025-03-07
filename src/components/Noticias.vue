<template>
  <div class="noticias">
    <h1>Últimas Notícias</h1>
    <ul>
      <li v-for="noticia in noticias" :key="noticia.id">
        <h2>{{ noticia.titulo }}</h2>
        <p>{{ noticia.descricao }}</p>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "NoticiasPage",
  data() {
    return {
      noticias: [
        { id: 1, titulo: "Notícia 1", descricao: "Descrição da notícia 1." },
        { id: 2, titulo: "Notícia 2", descricao: "Descrição da notícia 2." },
        { id: 3, titulo: "Notícia 3", descricao: "Descrição da notícia 3." },
      ],
    };
  },
};
</script>

<style scoped>
.noticias {
  font-family: Arial, sans-serif;
  padding: 20px;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  margin-bottom: 20px;
}
h2 {
  color: #2c3e50;
}
</style>
