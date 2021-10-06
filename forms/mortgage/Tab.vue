<template>
  <div v-show="isActive"><slot></slot></div>
</template>

<script>
export default {
  name: "Tab",
  props: {
    selected: {
      default: false,
      type: Boolean
    }
  },
  data() {
    return {
      isActive: false
    };
  },
  watch: {
    selected: "assignSelected"
  },
  created() {
    this.assignSelected();
  },
  methods: {
    assignSelected() {
      this.isActive = this.selected;
    }
  }
};
</script>

<style scoped></style>
