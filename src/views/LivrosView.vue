<script>
import axios from "axios";
export default {
  data() {
    return {
      livro: {},
      livros: [],
      autores: [],
      categorias: [],
      editoras: [],
    };
  },
  async created() {
    await this.buscarTodosOsAutores();
    await this.buscarTodasAsCategorias();
    await this.buscarTodasAsEditoras();
    const livros = await axios.get(
      "http://localhost:4000/livros?expand=categoria&expand=editora&expand=autor"
    );
    this.livros = livros.data;
  },

  methods: {
    async buscarTodosOsAutores() {
      const autores = await axios.get("http://localhost:4000/autores");
      this.autores = autores.data;
    },
    async buscarTodasAsCategorias() {
      const categorias = await axios.get("http://localhost:4000/categorias");
      this.categorias = categorias.data;
    },
    async buscarTodasAsEditoras() {
      const editoras = await axios.get("http://localhost:4000/editoras");
      this.editoras = editoras.data;
    },
    async buscarTodosOsLivros() {
      const livros = await axios.get("http://localhost:4000/livros");
      this.livros = livros.data;
    },

    async salvar() {
      const novo_livro = await axios.post(
        "http://localhost:4000/livros",
        this.livro
      );
      this.livros.push(novo_livro.data);
      this.livro = {};
    },
    async excluir(livro) {
      await axios.delete(`http://localhost:4000/livros/${livro.id}`);
      const indice = this.livros.indexOf(livro);
      this.livros.splice(indice, 1);
    },
    async atualizarLivro(livro) {
      const response = await axios.put(
        `http://localhost:4000/livros/${livro.id}`,
        livro,
      );
      return response.data;
    },
  },
};
</script>

<template>
  <div class="position-absolute top-50 start-50 translate-middle">
    <div class="formulario">
      <div>
        <h2 class="title">Gerencimento de Livros</h2>
      </div>
      <div class="input-group container">
        <input
          class="col form-control d-flex"
          type="text"
          v-model="livro.nome"
          @keyup.enter="salvar"
          placeholder="Livros"
        />
        <select v-model="livro.autorId" @keyup.enter="salvar">
          <option v-for="autor in autores" :key="autor.id" :value="autor.id">
            {{ autor.nome }}
          </option>
        </select>

        <select v-model="livro.categoriaId" @keyup.enter="salvar">
          <option
            v-for="categoria in categorias"
            :key="categoria.id"
            :value="categoria.id"
          >
            {{ categoria.nome }}
          </option>
        </select>

        <select v-model="livro.editoraId" @keyup.enter="salvar">
          <option v-for="editora in editoras" :key="editora.id" :value="editora.id">
            {{ editora.nome }}
          </option>
        </select>

        <button class="btn btn_save" @click="salvar">Salvar</button>
      </div>
    </div>
    <div class="list-items">
      <table>
        <thead>
          <tr>
            <td>ID</td>
            <td>Livros</td>
            <td>Autores</td>
            <td>Categorias</td>
            <td>Editoras</td>
            <td>Ações</td>
          </tr>
        </thead>
        <tbody>
          <tr v-for="livro in livros" :key="livro.id">
            <td>{{ livro.id }}</td>
            <td>{{ livro.title }}</td>
            <td>{{ livro.autorId }}</td>
            <td>{{ livro.categoryId }}</td>
            <td>{{ livro.publisherId }}</td>

            <td class="button-group d-flex salvar_editar">
              <button class="btn btn-primary" @click="alerta(livro)">Editar</button>
              <button class="btn btn-danger" @click="excluir(livro)">Excluir</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<style></style>
