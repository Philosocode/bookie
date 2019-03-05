<template>
  <ul class="book-list" v-if="books.length > 0">
    <Book
      v-for="book in books"
      :key="book.id"
      :title="book.title"
      :by="book.by"
      :publisher="book.publisher"
      :imageUrl="book.imageUrl"
      :bookUrl="book.bookUrl"
    />
  </ul>
</template>

<script>
import Book from "./Book";

export default {
  name: "BookList",
  props: ["books"],
  components: { Book }
}
</script>

<style scoped>
</style>