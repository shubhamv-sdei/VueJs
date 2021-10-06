<template>
  <div>
    <b-row class="detailModalSections">
      <b-col class="propery-images d-none d-lg-block d-xl-block" cols="7">
        <ImageScroller :images="mappedImagePaths" />
      </b-col>
      <b-col cols="12" sm="12" md="12" lg="5" xl="5" class="detail">
        <ListingDetailRight :listing="listing" />
      </b-col>
    </b-row>
    <div class="detailModalBg"></div>
    <div class="closeModal">
      <img src="@/assets/icons/close.svg" @click="close()" />
    </div>
  </div>
</template>

<script>
import ImageScroller from "./ImageScroller.vue";
import ListingDetailRight from "./ListingDetailRight.vue";
export default {
  name: "DetailModal",
  components: {
    ImageScroller,
    ListingDetailRight
  },
  props: {
    listing: {
      type: Object,
      default: () => {
        return {};
      }
    }
  },
  computed: {
    mappedImagePaths() {
      if (!this.listing) return null;
      return this.listing.imageList.map((l) => {
        return this.listing.imageBaseURL + l;
      });
    }
  },
  methods: {
    close() {
      this.$emit("onclose");
    }
  }
};
</script>
