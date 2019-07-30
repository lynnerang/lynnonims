<template>
  <div id="app">
    <AppHeader @onSearch="getWordList" :term="term" />
    <Results 
      @onTermClick="getWordList" 
      :wordList="wordList" 
      :defs="definitions" 
      :error="error"
      :loading="loading"
    />
  </div>
</template>

<script>
import AppHeader from './components/AppHeader';
import Results from './components/Results';

export default {
  name: 'app',
  components: {
    AppHeader,
    Results
  },
  data() {
    return {
      loading: false,
      term: '',
      error: '',
      definitions: [],
      wordList: []
    }
  },
  methods: {
    resetData() {
      this.definitions = [];
      this.wordList = [];
      this.loading = true;
      this.error = '';
    },

    getWordList: async function(term) {
      this.resetData();
      this.term = term;

      const res = await fetch(`${process.env.VUE_APP_ROOT_URL}/${term}?key=${process.env.VUE_APP_API_KEY}`)
      const data = await res.json();

      if (typeof data[0] === 'string') {
        this.error = 'Word not found.  Did you mean...?';
        this.wordList = data;
      } else if (data.length) {
        this.definitions = data[0].shortdef;
        this.wordList = data[0].meta.syns.reduce((acc, arr) => {
          arr.forEach(syn => {
            if (!acc.includes(syn)) acc.push(syn);
          })
          return acc;
        }, []);
      } else {
        this.error = 'Invalid entry, please try another word.';
      }

      this.loading = false;
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

</style>
