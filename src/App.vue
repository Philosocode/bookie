<template>
  <div class="container">
    <h1 class="main-heading">Bookie</h1>
    <SearchBar @search="onSearch($event)" @error="errorMessage = $event"/>
    <p v-show="errorMessage" class="error">{{ errorMessage }}</p>
    <br>
    <Spinner v-if="isLoading"/>
    <BookList :books="books" />
    <p v-show="statusMessage" class="status">{{ statusMessage }}</p>
  </div>
</template>

<script>
import axios from "axios";

import BookList from "./components/BookList";
import SearchBar from "./components/SearchBar";
import Spinner from "./components/Spinner";

export default {
  name: "App",
  data() {
    return {
      currentQuery: "",
      totalNumberOfBooks: 0,
      isLoading: false,
      statusMessage: "",
      errorMessage: ""
    };
  },
  components: {
    BookList,
    SearchBar,
    Spinner
  },
  methods: {
    onSearch(query) {
      this.currentQuery = this.formatQuery(query);

      // Clear state from previous requests
      this.resetSearchState();

      this.getBooks();
    },
    formatQuery(q) {
      return q.split(" ").join("+");
    },
    resetSearchState() {
      this.books = [];
      this.statusMessage = "";
      this.errorMessage = "";
      this.startIndex = 0;
    },
    async getBooks() {
      try {
        const res = await this.sendRequest(this.currentQuery, this.startIndex);
        this.processData(res.data);
      } catch (err) {
        this.errorMessage = err;
        return;
      } finally {
        this.isLoading = false;
      }
    },
    sendRequest(query, startIndex) {
      this.isLoading = true;

      return axios({
        method: "GET",
        url: `https://www.googleapis.com/books/v1/volumes?q=${query}&startIndex=${startIndex}`,
        timeout: 7500, // Reject promise if > 7.5 seconds
        headers: {
          "Content-Type": "application/json"
        }
      });
    },
    processData(data) {
      if (data.totalItems === 0) {
        this.statusMessage = "No books found :/";
        return;
      }
      const books = this.getBooksFromData(data.items);

      this.books = books;
      this.totalNumberOfBooks = data.totalItems;
    },
    getBooksFromData(booksData) {
      return booksData.map(bookData => {
        const id = bookData.id;
        const volume = bookData.volumeInfo;

        const title = volume.title;
        const publisher = volume.publisher || "Unknown Publisher";
        const bookUrl = volume.infoLink;

        let imageUrl;
        if (volume.imageLinks && volume.imageLinks.thumbnail) {
          imageUrl = volume.imageLinks.thumbnail;
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

.status {
  color: $color-green;
}

.error {
  color: $color-red;
}
</style>