<template>
  <div class="main" @click="$emit('on-click')">
    <img
      :src="'/assets/icons/' + source"
      :width="w"
      :height="h"
      class="inner"
    />
  </div>
</template>
<script>
export default {
  name: "ImageButton",
  props: {
    source: {
      type: String,
      default: ""
    },
    w: {
      type: Number,
      default: 0
    },
    h: {
      type: Number,
      default: 0
    }
  }
};
</script>
