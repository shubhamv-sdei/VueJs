<template>
  <div class="breadcrumb-wrapper">
    <section class="container">
      <b-row>
        <b-breadcrumb :items="breadCrumbs"></b-breadcrumb>
      </b-row>
    </section>
  </div>
</template>

<script>
export default {
  name: "Breadcrumb",
  props: {
    breadItems: {
      type: Array,
      default: () => {
        return [];
      }
    }
  },
  data() {
    return {
      breadCrumbs: this.breadItems
    };
  }
};
</script>
<style scoped>
@media (min-width: 1024px) {
ol.breadcrumb {
top: 2.6em;
position: relative;
}
}
section.container.mrg-top {
margin-bottom: 3em;
}
</style>
