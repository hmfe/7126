<template>
  <component :is="headingLevel" class="TypographyHeadingPrimary">{{ text }}</component>
</template>

<script>
export default {
  name: "TypographyHeadingPrimary",
  props: {
    text: {
      type: String,
      default: ""
    },
    headingImportance: {
      type: Number,
      default: 1
    }
  },
  computed: {
    headingLevel() {
      // Headings can only be h1-h6
      if (this.headingImportance < 1) return "h1";
      if (this.headingImportance > 6) return "h6";
      return `h${this.headingImportance}`;
    }
  }
};
</script>

<style lang="scss">
@import "../../../globals/styles/layout/layout__variables";

.TypographyHeadingPrimary {
  font-size: 2rem;
  margin-bottom: $layout__gutter;
}
</style>

