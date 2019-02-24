<template>
  <div class="container">
    <h1>Bookie</h1>
    <div class="input-container">
      <input type="text" v-model="searchTerm">
      <span v-if="searchTerm.length > 0" @click="searchTerm = ''" class="clear">X</span>
    </div>
    <button @click="onSubmit">Search</button>
    <p v-show="errorMessage" class="error">{{ errorMessage }}</p>
    <br>
    <ul v-if="books.length > 0">
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
    <p v-else>{{ statusMessage }}</p>
  </div>
</template>

<script>
import axios from "axios";

import Book from "./components/Book";

export default {
  name: "App",
  data() {
    return {
      searchTerm: "",
      books: [],
      paginationIndex: 10,
      totalNumberOfBooks: 0,
      statusMessage: "",
      errorMessage: ""
    };
  },
  computed: {
    query: function() {
      return this.searchTerm.split(" ").join("+");
    }
  },
  components: {
    Book
  },
  methods: {
    resetQuery() {
      this.books = [];
      this.statusMessage = "";
      this.errorMessage = "";
      this.paginationIndex = 10;
    },
    onSubmit() {
      this.resetQuery();

      if (!this.searchTerm || !this.searchTerm.replace(/\s/g, "").length) {
        this.errorMessage = "Enter something please...";
        return;
      }

      axios
        .get(`https://www.googleapis.com/books/v1/volumes?q=${this.query}`)
        .then(res => {
          if (!res.data.items) {
            this.statusMessage = "No books found";
            return;
          }

          const booksData = res.data.items;
          const books = booksData.map(bookData => {
            const id = bookData.id;
            const volume = bookData.volumeInfo;

            const title = volume.title;
            const publisher = volume.publisher || "Unknown Publisher";
            const bookUrl = volume.infoLink;

            let imageUrl;
            if (volume.imageLinks && volume.imageLinks.thumbnail) {
              imageUrl = volume.imageLinks.thumbnail;
            } else {
              imageUrl = "https://b.kisscc0.com/20180817/zzq/kisscc0-empty-book-intentionally-blank-page-book-cover-col-blank-book-5b76dcc0d16560.5728512315345164168577.png";
            }

            let by;
            if (!volume.authors) {
              by = "Unknown";
            } else if (volume.authors.length > 1) {
              by = volume.authors[0];
            } else {
              by = volume.authors.join(", ");
            }

            return { id, title, by, publisher, imageUrl, bookUrl };
          });

          this.totalNumberOfBooks = res.data.totalItems;
          this.books = books;
        })
        .catch(err => console.log(err));
    }
  }
};
</script>

<style lang="scss">
.container {
  margin: 0 auto;
  text-align: center;
  width: 80%;
}

.input-container {
  display: inline-block;
  position: relative;
  // overflow: hidden;
}

.clear {
  cursor: pointer;
  position: absolute;
  top: 0;
  right: .5rem;
  transition: all .2s ease-in;
}

.status {
  color: green;
}

.error {
  color: red;
}
</style>