<template>
  <main class="App" id="app">
    <header class="App__header">
      <TypographyHeadingPrimary text="Hitta favoritfilmen"/>
    </header>
    <section class="App__movie-search">
      <form class="App__movie-serch-form" @submit="submitHandler" ref="movieSearchForm">
        <InputSearch class="App__movie-serch-input" id="movie-search" name="movieSearch" />
        <button class="App__movie-search-submit" type="submit">Sök</button>
      </form>
      <div class="App__search-history">
        <ol class="App__search-history-items" v-if="historyItems.length > 0">
          <li v-for="item in historyItems" class="App__search-history-item" :key="item.id">
            <span class="App__search-history-term">
              {{ item.term }}
              <time
                class="App__search-history-time"
                :datetime="item.dateTime"
                aria-label="Sökningen gjordes"
              >{{ item.dateTime }}</time>
            </span>
            <button class="App__search-history-delete" @click="deleteHistoryItem(item.id)">Ta bort</button>
          </li>
        </ol>
      </div>
    </section>
    <section class="App__super-duper-button-wrapper">  
      <TypographyHeadingPrimary text="Superduper button" :headingImportance="2"/>
      <button class="SuperDuperButton">Delete</button>
    </section>
  </main>
</template>

<script>
import InputSearch from "./components/_input/InputSearch/InputSearch.vue";
import TypographyHeadingPrimary from "./components/_typography/TypographyHeadingPrimary/TypographyHeadingPrimary.vue";

export default {
  name: "app",
  components: {
    InputSearch,
    TypographyHeadingPrimary
  },
  data() {
    return {
      historyItems: []
    };
  },
  methods: {
    submitHandler(e) {
      e.preventDefault();
      this.addHistoryItem(
        new FormData(this.$refs.movieSearchForm).get("movieSearch"),
        this.getDateTime()
      );
    },
    getDateTime() {
      const now = new Date();
      return (
        now.getFullYear() +
        "-" +
        (now.getMonth() + 1) +
        "-" +
        now.getDate() +
        " " +
        now.getHours() +
        ":" +
        now.getMinutes() +
        ":" +
        now.getSeconds()
      );
    },
    addHistoryItem(term, dateTime) {
      this.historyItems = [
        {
          term: term,
          dateTime: dateTime,
          id: `${term.toLowerCase().replace(/ /g, "")}-${term.replace(
            / /g,
            ""
          )}`
        },
        ...this.historyItems
      ];
    },
    deleteHistoryItem(id) {
      this.historyItems = this.historyItems.filter(item => item.id !== id);
    }
  }
};
</script>

<style lang="scss">
@import "globals/styles/base/base__reset";
@import "globals/styles/layout/layout__variables";
@import "globals/styles/ui/ui__variables";

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}

.App {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.App__movie-search {
  width: calc(100vw - #{$layout__gutter * 2});
  margin-bottom: $layout__gutter * 2;

  @media (min-width: 800px) {
    width: 50vw;
    margin-bottom: $layout__gutter * 4;
  }
}

.App__movie-serch-form {
  display: flex;
  justify-content: flex-start;
  align-items: center;
}

.App__movie-serch-input {
  flex: 1;
}

.App__movie-search-submit {
  margin-left: $layout__gutter;
  color: white;
  background: $ui__color--interactive;
  padding: $layout__gutter / 2 $layout__gutter;
  border-radius: $layout__gutter;
  font-weight: bold;
  cursor: pointer;
  transition: background .2s;

  &:focus {
    outline: 0;
  }

  &:hover,
  &:focus {
    background: darken($ui__color--interactive, 10%);
  }
}

.App__search-history-items {
  width: 100%;
  margin-top: $layout__gutter * 2;
  border: 1px solid $ui__color--background-alt;
}

.App__search-history-item {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  padding: $layout__gutter;

  &:nth-child(even) {
    background: lighten($ui__color--background-alt, 5%);
  }
}

.App__search-history-time {
  font-size: 0.75rem;
  margin-top: $layout__gutter / 2;
  display: block;
}

.App__search-history-delete {
  font-size: 0.75rem;
  margin-left: auto;
  cursor: pointer;
  color: $ui__color--interactive;
  font-weight: bold;

  &:focus {
    outline: 0;
  }

  &:hover,
  &:focus {
    text-decoration: underline;
  }
}

.App__super-duper-button-wrapper {
  display: flex;
  align-items: center;
  flex-direction: column;
}

.SuperDuperButton {
  font-family: sans-serif;
  font-weight: bold;
  background: #f3ac56;
  color: black;
  display: flex;
  align-items: center;
  padding: 5px;

  &::after {
    content: "";
    background: white url(data:image/svg+xml;base64,PHN2ZyB4bWxucz0naHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmcnIHZpZXdCb3g9JzAgMCA3OC40IDc4LjQnPjxwYXRoIGZpbGw9JyMwMDAnIGQ9J002OS4yIDBsLTMwIDMwLTMwLTMwTDAgOS4ybDMwIDMwLTMwIDMwIDkuMiA5LjIgMzAtMzAgMzAgMzAgOS4yLTkuMi0zMC0zMCAzMC0zMEw2OS4yIDB6Jy8+PC9zdmc+) no-repeat center;
    background-size: calc(100% - 15px);
    width: 30px;
    height: 30px;
    border-radius: 50%;
    display: flex;
    justify-content: center;
    align-items: center;
    margin-left: 5px;
  }
}
</style>
