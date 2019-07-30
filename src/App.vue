<template>
  <div id="app">
    <AppHeader @onSearch="getSynonyms" :clickedTerm="clickedTerm" />
    <main>
      <img src="./assets/spinner.gif" v-if="loading" class="spinner"/>
      <h2 v-if="definitions.length">Definition</h2>
      <ul>
        <li v-for="(def, i) in definitions" :key="i">"{{ def }}"</li>
      </ul>
      <h2 v-bind:class="{error: suggested}" v-if="synonyms.length">
        {{suggested ? 'Word not found. Did you mean...?' : 'Synonyms'}}
      </h2>
      <h2 v-bind:class="{error: error}" v-if="error">Invalid search term.</h2>
      <ul class="synonym-list">
        <li 
          class="synonym" 
          v-for="(word, i) in synonyms" 
          :key="i" 
          v-on:click="updateSearch"
        >
          {{ word }}
        </li>
      </ul>
    </main>
  </div>
</template>

<script>
import AppHeader from './components/AppHeader'

export default {
  name: 'app',
  components: {
    AppHeader
  },
  data() {
    return {
      loading: false,
      clickedTerm: '',
      error: '',
      definitions: [],
      synonyms: [],
      suggested: false,
    }
  },
  methods: {
    resetData() {
      this.suggested = false;
      this.definitions = [];
      this.synonyms = [];
      this.loading = true;
      this.error = '';
    },

    getSynonyms: async function(term) {
      this.resetData();

      const res = await fetch(`${process.env.VUE_APP_ROOT_URL}/${term}?key=${process.env.VUE_APP_API_KEY}`)
      const data = await res.json();

      if (typeof data[0] === 'string') {
        this.suggested = true;
        this.synonyms = data;
      } else if (data.length) {
        this.definitions = data[0].shortdef;
        this.synonyms = data[0].meta.syns.reduce((acc, arr) => {
          arr.forEach(syn => {
            if (!acc.includes(syn)) acc.push(syn);
          })
          return acc;
        }, []);
      } else {
        this.error = true;
      }
      this.loading = false;
    },

    updateSearch(e) {
      this.getSynonyms(e.target.innerText);
      this.clickedTerm = e.target.innerText;
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

.synonym-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  margin-top: 25px;
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

.error {
  color: rgb(131, 22, 22);
}

</style>
