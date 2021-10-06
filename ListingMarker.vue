<template>
  <div class="map-marker" :class="markerClass()">
    <span class="theme-font-semi-bold price">{{ priceText }}</span>
    <span v-if="!listing.hasOpenHouse" class="hasOpenHouse"></span>
    <span v-if="favoritedList" class="addedFavorite"></span>
  </div>
</template>

<script>
export default {
  name: "ListingMarker",
  props: {
    listing: {
      type: Object,
      default: () => {
        return {};
      }
    }
  },
  data() {
    return {
      state: "",
      mlsNum: "",
      favoritedList: false,
      pendingStatus: ""
    };
  },
  computed: {
    priceText() {
      // console.log(this.listing.openHouse);
      if (this.listing.listingPrice > 999999) {
        const mlPr = (this.listing.listingPrice / 1000000).toFixed(2);
        return `$${parseFloat(mlPr.toString())} M`;
      } else if (this.listing.listingPrice > 1000) {
        const kPr = (this.listing.listingPrice / 1000).toFixed(1);
        return `$${parseFloat(kPr.toString())} K`;
      }
      return "";
    },
    isFavorited() {
      return this.$store.getters.getfavoriteList;
    }
  },
  watch: {
    isFavorited(d) {
      this.checkValue();
    }
  },
  mounted() {
    this.checkValue();
    this.isPending();
  },
  updated() {
    this.checkValue();
  },
  methods: {
    checkValue() {
      const userAuth = localStorage.getItem("userToken");
      if (userAuth !== null) {
        const favList = this.isFavorited;

        if (favList.includes(this.listing.mls)) {
          this.favoritedList = true;
        } else {
          this.favoritedList = false;
        }
      }
    },
    markerClass() {
      const openHs = this.listing.hasOpenHouse ? "hasOpenHouse" : "";
      const pending = this.isPending();
      // const live = this.listing.type ? "liveStr" : "";
      return `${openHs} ${pending}`;
    },
    isPending() {
      const pendingArr = ["PB", "PF", "PI", "PS", "P"];
      if (pendingArr.includes(this.listing.status)) {
        return "isPending";
      }
      return "";
    }
  }
};
</script>
