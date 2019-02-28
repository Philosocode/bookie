<template>
  <div class="container">
    <h1>Bookie</h1>
    <form v-on:submit.prevent="onSubmit">
      <div class="input-container">
        <input type="text" v-model="searchTerm">
        <span v-if="searchTerm.length > 0" @click="searchTerm = ''" class="clear">X</span>
      </div>
      <button>Search</button>
    </form>
    <p v-show="errorMessage" class="error">{{ errorMessage }}</p>
    <br>
    <div class="loading" v-if="isLoading">LOADING...</div>
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
    <p v-else class="status">{{ statusMessage }}</p>
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
      totalNumberOfBooks: 0,
      isLoading: false,
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
  mounted() {
    this.scroll();
  },
  methods: {
    // Case: loading time takes too long
    resetQuery() {
      this.books = [];
      this.statusMessage = "";
      this.errorMessage = "";
      this.paginationIndex = 0;
    },
    onSubmit() {
      this.resetQuery();

      if (!this.searchTerm || !this.searchTerm.replace(/\s/g, "").length) {
        this.errorMessage = "Enter something please...";
        return;
      }

      this.getBooks();
    },
    getBooks() {
      this.isLoading = true;

      axios
        .get(
          `https://www.googleapis.com/books/v1/volumes?q=${
            this.query
          }&startIndex=${this.paginationIndex}&maxResults=40`
        )
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
              imageUrl =
                "https://b.kisscc0.com/20180817/zzq/kisscc0-empty-book-intentionally-blank-page-book-cover-col-blank-book-5b76dcc0d16560.5728512315345164168577.png";
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
        .then(() => {
          this.isLoading = false;
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

.loading { }

.input-container {
  display: inline-block;
  position: relative;
}

.clear {
  cursor: pointer;
  position: absolute;
  top: 0;
  right: 0.5rem;
  transition: all 0.2s ease-in;
}

.status {
  color: green;
}

.error {
  color: red;
}
</style>