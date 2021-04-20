<template>
  <div id="app">
    <div class="hero is-white is-gradient is-bold">
      <div class="hero-body">
        <div>
          <a href="/">
            <img src="./assets/rick_morty_logo.png" alt="" width="50%">
          </a>
        </div>

        <div class="field has-addons is-pulled-right">
          <div class="control">
            <input
              v-model="search"
              type="text"
              class="input is-rounded"
              v-on:keyup.enter="searchData"
            />
          </div>

          <div class="control">
            <button
              class="button is-success is-rounded"
              v-on:click="searchData"
            >
              Buscar
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="container">
      <div
        class="columns is-desktop is-mobile is-tablet is-multiline is-centered mt-1"
      >
        <character
          @showModal="showModal"
          :character="character"
          v-for="character of characters"
          :key="character.id"
        />
      </div>

      <!-- <div>
        <p>
          v-for="character of characters"
          v-bind:key="character.id"
          v-bind:character="character"
        </p>
      </div> -->

      <nav class="pagination" role="navegation" aria-label="pagination">
        <a
          href=""
          class="pagination-previous"
          @click.prevent="changePage(page - 1)"
          >Anterior</a
        >

        <ul class="pagination-list">
          <li>
            <a href="" class="pagination-link is-current"> {{ page }} </a>
          </li>
        </ul>

        <a href="" class="pagination-next" @click.prevent="changePage(page + 1)"
          >Siguiente</a
        >
      </nav>
    </div>

    <div class="modal" :class="{ 'is-active': modal }" v-if="modal">

      <div class="modal-background" @click="modal = false;"></div>

      <div class="modal-card">
        <div class="modal-card-head">
          <p class="modal-card-title">Acerca de: nombre personaje</p>
        </div>

        <p>Genero</p>
        <p>Estadoo</p>
        <p>Raza</p>
        <p>Tipo</p>

        <footer class="modal-card-food">
          <button class="button"></button>
        </footer>
      </div>
    </div>
  </div>
</template>

<script>
// librerias
import axios from "axios";

import Character from "./components/Character.vue";

export default {
  name: "App",
  components: {
    Character,
  },
  data: function() {
    return {
      characters: [],
      page: 1,
      pages: 1,
      search: "",
      modal: false,
      currentCharacter: {}
    };
  },
  created() {
    this.fetch();
  },
  methods: {
    fetch() {
      const params = {
        page: this.page,
        name: this.search,
      };

      let result = axios
        .get("https://rickandmortyapi.com/api/character/", { params })
        .then((res) => {
          this.characters = res.data.results;
          this.pages = res.data.info.pages;
          console.log(res.data);
        })
        .catch((err) => {
          console.log(err);
        });
    },
    changePage(page) {
      this.page = page <= 0 || page > this.pages ? this.page : page;

      // Update data
      this.fetch();
      // Scroll top
      window.scrollTo(0, 0);
    },
    searchData() {
      this.page = 1;
      this.fetch();
    },
    showModal (id) {
      this.fetchOne(id);
    },
    async fetchOne (id) {
      // llamada HTTP
      let result = await axios.get(`https://rickandmortyapi.com/api/${id}/`);
      this.currentCharacter = result.data;
      this.modal = true;

      // console.log(this.currentCharacter, "Personaje");
    },
  },
};
</script>
