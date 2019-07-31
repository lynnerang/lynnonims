<template>
  <header>
    <div>
      <img class="photo" v-bind:class="{show: showLynne}" src="../assets/lynne.png" aria-hidden />
    </div>
    <div>
      <h1>LYNNONIMS</h1>
      <img alt="Vue logo" src="../assets/dictionary.png" aria-hidden style="width: 160px" />
      <form @submit.prevent="handleSearch">
        <input
          class="txt-input"
          v-model="inputVal"
          placeholder="Search a word for synonyms..."
        />
        <input
          class="search-btn"
          value="Search"
          type="submit"
          :disabled="!inputVal"
        />
      </form>
    </div>
    <div>
      <img class="photo" v-bind:class="{show: showNim}" src="../assets/nim.png" aria-hidden />
    </div>
  </header>
</template>

<script>

export default {
  name: "AppHeader",
  props: ['term'],
  watch: { 
    term: function(newVal) { 
      this.inputVal = newVal;
    }
  },
  data() {
    return {
      inputVal: '',
      showLynne: false,
      showNim: false
    };
  },
  methods: {
    handleSearch: function() {
      this.flashImages();
      this.$emit("onSearch", this.inputVal);
    },
    flashImages() {
      this.showLynne = true;

      setTimeout(() => {
        this.showLynne = false;
        this.showNim = true;
      }, 500);

      setTimeout(() => (this.showNim = false), 1000);
    },
  }
};
</script>

<style>

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
  font-family: "BioRhyme", serif;
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
  background: #009ea0;
  color: white;
  cursor: pointer;
}

.search-btn:disabled {
  background: rgb(172, 172, 172) !important;
}

.show {
  visibility: visible;
}

.error {
  color: rgb(131, 22, 22);
}

@media screen and (max-width: 900px) {

  body {
    margin: 0;
    width: 100vw;
    box-sizing: border-box;
  }

  .photo,
  .show {
    display: none;
  }

  h1 {
    font-size: 36px;
  }

  .txt-input {
    width: 200px;
  }

  .search-btn {
    font-size: 16px;
  }

  ::placeholder {
    font-size: 14px;
  }
}
</style>
