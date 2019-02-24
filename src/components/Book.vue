<template>
  <div class="book">
    <h2>{{ title }}</h2>
    <p>By: {{ by }}</p>
    <p>Published By: {{ publisher }}</p>
    <img :src="imageUrl" :alt="title">
    <a class="book__link" :href="bookUrl" target="_blank" rel="noopener">
      <button>See This Book</button>
    </a>
  </div>
</template>

<script>
export default {
  name: 'Book',
  props: ["title", "by", "publisher", "imageUrl", "bookUrl"],
}
</script>

<style lang="scss" scoped>
.book {
  background: $color-grey-light-2;
  margin: 0 auto;
  width: 25rem;

  &__link {
    display: block;
  }
}
</style>
