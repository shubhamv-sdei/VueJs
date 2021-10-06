<template>
  <b-row
    :class="selected ? 'filter-box box selected' : 'filter-box box'"
    @click="clicked()"
  >
    <b-row>
      <b-row></b-row>
      <b-row>
        <b-col cols="4"></b-col>
        <b-col cols="4">
          <img
            class="img"
            :src="imagePath"
            :width="imageWidth"
            :height="imageHeight"
          />
        </b-col>
        <b-col cols="4"></b-col>
      </b-row>
    </b-row>
    <b-row no-gutters>
      <b-col cols="3"></b-col>
      <b-col cols="7" class="left">
        <span :class="'theme-font-light a ' + (selected ? 'b' : '')">{{
          title
        }}</span>
      </b-col>
    </b-row>
  </b-row>
</template>

<script>
export default {
  name: "FilterBox",
  props: {
    imagePath: {
      type: String,
      default: ""
    },
    title: {
      type: String,
      default: ""
    },
    imageWidth: {
      type: String,
      default: ""
    },
    imageHeight: {
      type: String,
      default: ""
    }
  },
  data() {
    return {
      selected: false
    };
  },
  methods: {
    clicked() {
      this.selected = !this.selected;
      this.$emit("selection-changed", this.selected);
    }
  }
};
</script>
