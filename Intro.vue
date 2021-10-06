<template>
  <b-row>
    <b-col cols="12" class="col-sm-12 col-md-12 pr-0 pl-0">
      <b-row class="intro-b theme-font-regular" v-html="data[1]"></b-row>
      <b-row no-gutters>
        <p class="intro-c fw-light" v-html="data[2]"></p>
      </b-row>
    </b-col>
  </b-row>
</template>

<script>
export default {
  name: "Intro",
  props: {
    data: {
      type: Array,
      default: () => {
        return [];
      }
    }
  }
};
</script>
