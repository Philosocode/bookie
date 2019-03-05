<template>
  <form @submit.prevent="processInput">
    <div class="search-bar__container">
      <div>
        <input class="search-bar__input" type="text" v-model="searchTerm">
        <span class="search-bar__clear" v-if="searchTerm.length > 0" @click="searchTerm = ''">X</span>
      </div>
      <button class="btn search-bar__btn">Search</button>
    </div>
  </form>
</template>

<script>
export default {
  data() {
    return {
      searchTerm: ""
    }
  },
  methods: {
    processInput() {
      if (!this.searchTerm || !this.searchTerm.replace(/\s/g, "").length) {
        this.$emit("error", "Enter something please...");
      } else {
        this.$emit('search', this.searchTerm);
      }
    },
  }
};
</script>

<style lang="scss" scoped>
.search-bar {
  &__container {
    position: relative;
    height: 4rem;
  
    display: flex;
    flex-direction: row;
    justify-content: center;
  }

  &__input {
    border-top-left-radius: 3px;
    border-bottom-left-radius: 3px;
    border: 1px solid transparent;
    height: 100%;
    padding: 0 1rem;
    position: relative;

    @include respond(tab-port) {
      width: 33rem;
    }
  }

  &__clear {
    cursor: pointer;
    display: inline-block;
    position: absolute;
    transform: translateX(-2rem) translateY(.8rem);
    transition: all 0.2s ease-in;
  }

  &__btn {
    height: 100%;
    border-top-left-radius: 0;
    border-bottom-left-radius: 0;
  }
}
</style>