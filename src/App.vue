<template>
  <div id="app">
    <header>
      <div><img class="photo" v-bind:class="{show: showLynne}" src="./assets/lynne.png" aria-hidden/></div>
      <div>
        <h1>LYNNONIMS</h1>
        <img alt="Vue logo" src="./assets/dictionary.png" aria-hidden style="width: 160px">
        <form @submit.prevent="getSynonyms">
          <input class="txt-input" v-model="searchTerm" placeholder="Search a word for synonyms..."/>
          <input class="search-btn" value="Search" type="submit" v-bind:disabled="!searchTerm"/>
        </form>
      </div>
      <div><img class="photo" v-bind:class="{show: showNim}" src="./assets/nim.png" aria-hidden /></div>
    </header>
    <main>
      <img src="./assets/spinner.gif" v-if="loading" class="spinner"/>
      <h2 v-if="definitions.length">Definition</h2>
      <ul>
        <li v-for="(def, i) in definitions" :key="i">"{{ def }}"</li>
      </ul>
      <h2 v-if="synonyms.length">{{showSuggestions ? 'Word not found. Did you mean...?' : 'Synonyms'}}</h2>
      <ul class="synonym-list">
        <li class="synonym" v-for="(word, i) in synonyms" :key="i" v-on:click="updateSearch">{{ word }}</li>
      </ul>
    </main>
  </div>
</template>

<script>
import { setTimeout } from 'timers';

export default {
  name: 'app',
  data() {
    return {
      loading: false,
      searchTerm: '',
      definitions: [],
      synonyms: [],
      showSuggestions: false,
      showLynne: false,
      showNim: false
    }
  },
  methods: {
    getSynonyms: async function() {
      this.flashImages();

      this.showSuggestions = false;
      this.definitions = [];
      this.synonyms = [];
      this.loading = true;

      const res = await fetch(`${process.env.VUE_APP_ROOT_URL}/${this.searchTerm}?key=${process.env.VUE_APP_API_KEY}`)
      const data = await res.json();

      if (typeof data[0] === 'string') {
        this.showSuggestions = true;
        this.synonyms = data;
      } else {
        this.definitions = data[0].shortdef;
        this.synonyms = data[0].meta.syns.reduce((acc, arr) => {
          arr.forEach(syn => {
            if (!acc.includes(syn)) acc.push(syn);
          })
          return acc;
        }, []);
      }
      this.loading = false;
    },

    updateSearch(e) {
      this.searchTerm = e.target.innerText;
      this.getSynonyms();
    },

    flashImages() {
      this.showLynne = true;
      setTimeout(() => {
        this.showLynne = false;
        this.showNim = true;
      }, 500);
      setTimeout(() => this.showNim = false, 1000);
    }
  }
}
</script>

<style>

* {
  box-sizing: border-box;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 30px;
}

.spinner {
  height: 100px;
  margin-top: 50px;
}

header {
  display: flex;
  width: 100vw;
  justify-content: space-around;
}

.photo {
  height: 300px;
  visibility: hidden;
}

h1 {
  margin-bottom: 10px;
  margin-top: 0;
  font-size: 46px;
  font-family: 'BioRhyme', serif;
  font-weight: 400;
}

form {
  margin-top: 20px;
  align-items: center;
}

input {
  height: 36px;
  box-sizing: border-box;
  font-size: 18px;
}

h2 {
  margin-bottom: -10px;
  margin-top: 25px;
  font-size: 22px;
}

ul {
  list-style: none;
  padding: 0;
}

.txt-input {
  border-radius: 5px 0 0 5px;
  padding-left: 8px;
  width: 250px;
}

::placeholder {
  font-style: italic;
  color: rgb(169, 169, 169);
  font-size: 15px;
}

.search-btn {
  height: 36px;
  border-radius: 0 5px 5px 0;
  border: none;
  background: #009EA0;
  color: white;
  cursor: pointer;
}

.search-btn:hover {
  background: #2d3134;
}

.synonym-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.synonym {
  font-size: 28px;
  margin: 15px 40px;
  color: #009EA0;
  padding: 0 10px;
  background: rgb(245, 245, 245);
  cursor: pointer;
}

.synonym:hover {
  background: #009EA0;
  color: white;
}

.show {
  visibility: visible;
}

</style>
