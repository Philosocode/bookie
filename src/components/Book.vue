<template>
  <div class="book">
    <div class="book__thumbnail-container">
      <img v-if="imageUrl" class="book__thumbnail" :src="imageUrl" :alt="title">
      <img v-else class="book__thumbnail" src="@/assets/book-cover.jpg" :alt="Title">
    </div>

    <div class="book__divider"></div>

    <div class="book__content">
      <h2 class="book__title">{{ shortenedTitle }}</h2>
      <p class="book__by">By: {{ by }}</p>
      <p class="book__publisher">Publisher: {{ publisher }}</p>
      <a class="book__link" :href="bookUrl" target="_blank" rel="noopener">
        <button class="btn book__btn">See This Book</button>
      </a>
    </div>

  </div>
</template>

<script>
export default {
  name: 'Book',
  props: {
    title: String,
    by: String,
    publisher: String,
    bookUrl: String,
    imageUrl: {
      type: String
    }
  },
  computed: {
    shortenedTitle() {
      if (this.title.length < 55) {
        return this.title;
      } else {
        let shortenedTitle = this.title.slice(0, 50);
        const lastCharIndex = shortenedTitle.length - 1;
        const lastChar = shortenedTitle[lastCharIndex];

        if (lastChar === " ") {
          shortenedTitle[lastCharIndex] = ".";
          shortenedTitle += "..";
        } else {
          shortenedTitle += "...";
        }

        return shortenedTitle;
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.book {
  background: $color-grey-light-1;
  border-radius: 5px;
  margin: 3rem auto;
  padding: 2rem 0;
  width: 20rem;

  display: flex;
  flex-direction: column;

  @include respond(tab-port) {
    flex-direction: row;
    width: 45rem;
  }

  @include respond(tab-land) {
    width: 60rem;
  }

  &:first-child {
    margin-top: 2rem;
  }

  &__thumbnail-container {
    border-radius: 3px;
    max-width: 13rem;
    max-height: 20rem;
    margin: 0 auto;

    display: flex;
    align-self: center;
  }

  &__thumbnail {
    max-height: 100%;
    max-width: 100%;
  }

  &__divider {
    background-color: darken($color-grey-light-2, 5%);
    margin: 2rem auto -1rem auto;
    height: 1px;
    width: 100%;

    @include respond(tab-port) {
      display: none;
    }

    @include respond(tab-land) {
      display: block;
      height: 20rem;
      margin: 1.5rem 1.5rem 1.5rem 0;
      width: 1px;
    }
  }

  &__content {
    padding-left: 1rem;
    padding-right: 1rem;
    text-align: center;

    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;

    @include respond(tab-port) {
      align-items: flex-start;
      text-align: left;
      width: 66%;
    }
  }

  &__title {
    font-size: 2rem;
    margin-bottom: -1rem;

    @include respond(tab-port) {
      font-size: 2.4rem;
      margin-bottom: -1.5rem;
    }
  }

  &__by {
    margin-bottom: -1rem;
    font-size: 1.5rem;
    font-style: italic;

    @include respond(tab-port) {
      font-size: 1.8rem;
      margin-bottom: -1rem;
    }
  }

  &__publisher {
    font-size: 1.5rem;

    @include respond(tab-port) {
      font-size: 1.6rem;
    }
  }

  &__link {
    display: block;
  }

  &__btn {
    width: 14rem;
  }
}
</style>
