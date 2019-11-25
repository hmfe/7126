<template>
  <div class="A11yScreenReaderBroadcast" :aria-live="liveType">
    <span v-if="text" class="A11yScreenReaderBroadcast__text">{{ text }}</span>
  </div>
</template>

<script>
export default {
  name: "InputSearch",
  data() {
    return {
      autocompleteItems: []
    };
  },
  props: {
    text: String,
    liveType: {
      type: String,
      default: "polite"
    }
  }
};
</script>

<style>
.InputSearch__instructions {
  display: none;
}
</style>
