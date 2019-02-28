<template>
  <div class="book">
    <h2>{{ title }}</h2>
    <p>By: {{ by }}</p>
    <p>Published By: {{ publisher }}</p>
    <img class="book__image" :src="imageUrl" :alt="title">
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
  display: inline-block;
  margin: 2rem 3rem;
  padding: .25rem 0 3rem 0;
  width: 25rem;

  &__image {
    height: 17.5rem;
    max-height: 17.5rem;
    width: 12.8rem;
    max-width: 12.8rem;
  }

  &__link {
    display: block;
  }
}
</style>
