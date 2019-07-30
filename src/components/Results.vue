<template>
  <main>
    <img src="../assets/spinner.gif" v-if="loading" class="spinner"/>
    <h2 v-if="defs.length">Definition</h2>
    <ul>
      <li v-for="(def, i) in defs" :key="i">"{{ def }}"</li>
    </ul>
    <h2 v-if="!loading" v-bind:class="{error: error}">
      {{error ? error : 'Synonyms'}}
    </h2>
    <ul class="synonym-list">
      <li 
        class="synonym" 
        v-for="(word, i) in wordList" 
        :key="i" 
        v-on:click="handleTermClick"
      >
        {{ word }}
      </li>
    </ul>
  </main>
</template>

<script>

export default {
  name: 'Results',
  props: ['term', 'loading', 'error', 'wordList', 'defs'],
  methods: {
    handleTermClick(e) {
      this.$emit('onTermClick', e.target.innerText);
    }
  }
}
</script>

<style>

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