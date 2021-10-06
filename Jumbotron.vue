<template>
  <b-row style="background-color: #fff;">
    <img :src="'assets/' + name" width="100%" class="cover" />
  </b-row>
</template>
<script>
export default {
  name: "Jumbotron",
  props: {
    name: {
      type: String,
      default: ""
    }
  }
};
</script>

<style lang="scss" scoped>
.cover {
  object-fit: cover;
  max-height: 920px;
  min-height: 345px;
}
@media screen and (max-width: 800px) {
  .cover {
    height: 350px;
  }
}
</style>
