<template>
  <div class="InputSearch" :class="modifiers">
    <input
      type="text"
      class="InputSearch__input"
      :name="name"
      :id="id"
      :aria-describedby="instructionsId"
      :aria-owns="resultsId"
      autocomplete="off"
      aria-expanded="false"
      aria-autocomplete="both"
      :aria-activedescendant="activeDescendant"
      @input="inputHandler"
      :value="value"
      placeholder="Sök på titel"
    >    

    <transition name="InputSearch__transition--results">
      <ul
        :id="resultsId"
        class="InputSearch__results-items"
        role="listbox"
        tabindex="0"
        v-show="showSearchItems"
      >
        <li
          v-for="(item, index) in resultItems"
          class="InputSearch__results-item"
          :id="getSelectedId(index)"
          :key="item.imdbID"
          :aria-selected="getSelectedOption(index)"
          tabindex="-1"
          role="option"
          @click="() => resultItemClickHandler(index)"
        >{{ item.Title }}</li>
      </ul>
    </transition>
    <span
      class="InputSearch__instructions"
      :id="instructionsId"
    >Navigera i sökförslagen genom att använda upp- och nerpilarna.</span>

    <!-- TODO: Add screen reader announcements -->
    <A11yScreenReaderBroadcast :text="screenReaderText" liveType="assertive"/>
  </div>
</template>

<script>
import A11yScreenReaderBroadcast from "../../_a11y/A11yScreenReaderBroadcast/A11yScreenReaderBroadcast.vue";
export default {
  name: "InputSearch",
  components: {
    A11yScreenReaderBroadcast
  },
  data() {
    return {
      resultItems: [],
      screenReaderText: "",
      activeItemId: "",
      activeItemIndex: "",
      value: ""
    };
  },
  props: {
    id: {
      type: String,
      required: true
    },
    name: {
      type: String,
      required: true
    }
  },
  mounted() {
    this.addSubmitHandler()
  },
  methods: {
    // On search input
    async inputHandler(e) {
      this.value = e.target.value;
      if (e.target.value.length < 2) return;

      try {
        const results = await fetch(
          `http://www.omdbapi.com/?apikey=4894009d&s=${e.target.value}`
        );

        const json = await results.json();

        if (!json.Search) {
          this.resultItems = [];
        } else {
          this.resultItems = json.Search;
        }
      } catch (error) {
        this.resultItems = [];
      }
    },

    // Add keyboard handler for arrow navigation in results list
    addKeyboardHandler() {
      document.addEventListener("keydown", this.keydownHandler);
    },

    // Remove keyboard handler for arrow navigation in results list
    removeKeyboardHandler() {
      document.removeEventListener("keydown", this.keydownHandler);
    },

    // Keyboard handler for arrow navigation in results list
    keydownHandler(e) {
      const keyboard = {
        enter: 13,
        up: 38,
        down: 40
      };

      switch (e.keyCode) {
        case keyboard.down:
          this.keyboardDownHandler();
          break;
        case keyboard.up:
          this.keyboardUpHandler();
          break;
      }
    },
    keyboardDownHandler() {
      // If no active item is set or if active item is the last item in the list, set active item to the first item in the list
      if (
        !this.activeItemId ||
        this.activeItemId ===
          this.resultItems[this.resultItems.length - 1].imdbID
      ) {
        this.activeItemIndex = 0;
      } else {
        this.activeItemIndex++;
      }
    },
    keyboardUpHandler() {
      // If no active item is set or if active item is the first item in the list, set active item to the last item in the list
      if (
        !this.activeItemId ||
        this.activeItemId === this.resultItems[0].imdbID
      ) {
        this.activeItemIndex = this.resultItems.length - 1;
      } else {
        this.activeItemIndex--;
      }
    },
    addSubmitHandler() {
      const parentForm = this.$el.closest('form');
      parentForm.addEventListener('submit', this.submitHandler);
    },
    submitHandler() {
      this.clearSearch();
    },
    resultItemClickHandler(index) {
      this.activeItemIndex = index;
    },
    setActiveItemId(index) {
      this.activeItemId = this.resultItems[index].imdbID;
    },
    setActiveItemValue() {
      this.value = this.resultItems[this.activeItemIndex].Title;
    },
    getSelectedId(index) {
      return index === this.activeItemIndex
        ? "InputSearch__results-item--selected"
        : false;
    },
    getSelectedOption(index) {
      return index === this.activeItemIndex;
    },
    clearSearch() {
      this.resultItems = [];
      this.value = '';
    }
  },
  computed: {
    modifiers() {
      return {};
    },
    resultsId() {
      return `${this.id}--results`;
    },
    instructionsId() {
      return `${this.id}--instructions`;
    },
    showSearchItems() {
      return this.resultItems.length > 0;
    },
    activeDescendant() {
      return this.activeItemIndex ? "InputSearch__results-item--selected" : "";
    }
  },
  watch: {
    resultItems(items) {
      if (items.length > 0) {
        this.addKeyboardHandler();
      } else {
        this.removeKeyboardHandler();
      }
    },
    activeItemIndex(index) {
      this.setActiveItemId(index);
      this.setActiveItemValue(index);
    }
  }
};
</script>

<style lang="scss">
@import "../../../globals/styles/ui/ui__variables";
@import "../../../globals/styles/layout/layout__variables";

.InputSearch {
  position: relative;
}

.InputSearch__input {
  text-align: center;
  width: 100%;
  padding: 1rem;
  font-size: 1rem;
  border-radius: 1.5rem;
  box-shadow: none;
  border: 1px solid $ui__color--background-alt;
  transition: box-shadow 0.2s;

  &:focus,
  &:hover {
    outline: none;
    box-shadow: 0px 2px 5px 0px rgba(0, 0, 0, 0.3);
  }
}

.InputSearch__instructions {
  display: none;
}

.InputSearch__results-items {
  position: absolute;
  top: 100%;
  left: 50%;
  width: 100%;
  transform: translateX(-50%);
  margin-top: $layout__gutter * 2;
  border: 1px solid $ui__color--background-alt;
  box-shadow: 0px 2px 5px 0px rgba(0, 0, 0, 0.2);
}

.InputSearch__results-item {
  background-color: $ui__color--background;
  padding: $layout__gutter / 2;
  cursor: pointer;

  &:hover,
  &[aria-selected] {
    background-color: $ui__color--background-alt;
  }
}

.InputSearch__transition--results-enter-active,
.InputSearch__transition--results-leave-active {
  transition: opacity 0.2s;
}

.InputSearch__transition--results-enter,
.InputSearch__transition--results-leave-to {
  opacity: 0;
}
</style>
