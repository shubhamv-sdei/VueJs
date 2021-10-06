<template>
  <b-row
    id="listGridContainer"
    ref="listingContainer"
    class="grid1"
    align-h="center"
  >
    <div
      v-if="
        propertyList !== undefined &&
          propertyList.length !== 0 &&
          beforeFullLoad === false
      "
      id="listingWrapperB"
      ref="listingWrapperB"
      class="row align-content-start"
    >
      <div id="TopOfTheListing" ref="TopOfTheListing">
        <a ref="linkTag" tabindex="0">a</a>
        <input ref="topInput" type="text" readonly />
      </div>
      <b-col
        v-for="(l, index) in propertyList"
        :id="'house' + l.mls"
        :key="l.mls"
        ref="houseGrid"
        cols="12"
        class="house-grid"
        :class="activeHouse == 'house' + l.mls ? 'selected' : ''"
        @mouseenter="highlightMarker(l.mls)"
      >
        <b-row>
          <ListingPreview
            style="cursor: pointer;"
            :data-id="index"
            :listing="l"
          />
        </b-row>
      </b-col>
      <div v-if="rows > perPage" class="col-12 listing-pagination">
        <b-pagination
          v-model="currentPage"
          :total-rows="rows"
          :per-page="perPage"
          aria-controls="my-table"
          first-text="First"
          prev-text="Previous"
          next-text="Next"
          last-text="Last"
          @change="navPagination"
        ></b-pagination>
      </div>
    </div>
    <div
      v-if="propertyList == undefined && beforeFullLoad === false"
      class="row listing-no-result align-content-start"
    >
      <div class="col-md-12">
        <h3 class="font-22 fw-600">No matching results</h3>
        <p class="font-15 mb-0">
          Edit or remove these filters for best results.
        </p>
        <a href="" class="link font-15 fw-500" @click.prevent="clearFilters"
          >Remove all filters</a
        >
      </div>
    </div>
    <div v-if="beforeFullLoad" class="row">
      <div class="col-md-12 pl-0">
        <div class="loading-container">
          <ul class="o-vertical-spacing o-vertical-spacing--l">
            <li class="blog-post o-media">
              <div class="o-media__figure">
                <span
                  class="skeleton-box"
                  style="width:100px;height:80px;"
                ></span>
              </div>
              <div class="o-media__body">
                <div class="o-vertical-spacing">
                  <h3 class="blog-post__headline">
                    <span class="skeleton-box" style="width:55%;"></span>
                  </h3>
                  <p>
                    <span class="skeleton-box" style="width:80%;"></span>
                    <span class="skeleton-box" style="width:90%;"></span>
                  </p>
                </div>
              </div>
            </li>
            <li class="blog-post o-media">
              <div class="o-media__figure">
                <span
                  class="skeleton-box"
                  style="width:100px;height:80px;"
                ></span>
              </div>
              <div class="o-media__body">
                <div class="o-vertical-spacing">
                  <h3 class="blog-post__headline">
                    <span class="skeleton-box" style="width:55%;"></span>
                  </h3>
                  <p>
                    <span class="skeleton-box" style="width:80%;"></span>
                    <span class="skeleton-box" style="width:90%;"></span>
                  </p>
                </div>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </b-row>
</template>

<script>
import ListingPreview from "@/components/favorites/FavListingPreview";
import { Helper } from "@/plugins/mixins/helper";

export default {
  name: "ListingsGrid",
  components: {
    ListingPreview
  },
  mixins: [Helper],
  data() {
    return {
      perPage: 20,
      currentPage: 1,
      totalProperty: 0,
      selectedMls: {},
      startPage: 0,
      activeHouse: "",
      beforeFullLoad: false,
      isSearchStarted: false,
      navigateFromPagination: 1,
      clicked: 0,
      propertyList: [],
      currList: [],
      listingChunk: [],
      currentList: []
    };
  },
  computed: {
    listings() {
      return this.$store.getters.getListings;
    },
    listingsChunk() {
      return this.$store.getters.getFavListingsChunck;
    },
    markerClicked() {
      return this.$store.getters.getSelectedListing;
    },
    rows() {
      return this.$store.getters.getFavTotalDataCount;
    },
    searchingStatus() {
      return this.$store.getters.getSearchStarted;
    },
    goToSelectedPage() {
      return this.$store.getters.getSelectedPage;
    },
    navigationStatus() {
      return this.$store.getters.getNavigateFromMap;
    },
    checkLoadSt() {
      return this.$store.getters.getPageLoadedSt;
    },
    getLoadingStatus() {
      return this.$store.getters.getApiLoadingStatus;
    },
    getSearchStatus() {
      return this.$store.getters.getSearchFierd;
    }
  },
  watch: {
    markerClicked(value) {
      if (value) {
        this.selectedMls = value;
        const $this = this;
        $this.highlightList($this.selectedMls);
      }
    },
    navigationStatus(st) {
      this.navigateFromPagination = st;
    },
    currentPage(d) {
      const $this = this;
      const pageData = this.listingsChunk;

      this.propertyList = pageData[d - 1];
      setTimeout(() => {
        if ($this.navigationStatus === 1) {
          setTimeout(() => {
            $this.$refs.topInput.focus();
          }, 300);
          setTimeout(() => {
            $this.$refs.linkTag.focus();
          }, 50);
        }
      }, 200);
    },
    rows(d) {
      this.totalProperty = d;
    },
    currentList(d) {
      this.currList = d;
    },
    listings(d) {
      this.beforeFullLoad = false;
    },
    searchingStatus(d) {
      this.isSearchStarted = d;
    },
    goToSelectedPage(nm) {
      this.currentPage = nm;
    },
    listingsChunk(lst) {
      this.propertyList = lst[0];
      this.listingChunk = lst;
    },
    getLoadingStatus(st) {
      this.beforeFullLoad = st;
    },
    getSearchStatus(st) {
      this.beforeFullLoad = st;
    }
  },
  mounted() {
    if (this.propertyList) {
      this.beforeFullLoad = false;
    } else {
      this.showCurrentList();
    }
    this.propertyList = this.showCurrentList();
  },
  updated() {
    const $this = this;
    const checkLoad = new Promise((resolve, reject) => {
      setTimeout(function() {
        resolve($this.listingsChunk);
      }, 1000);
    });
    if ($this.checkLoadSt === 1) {
      checkLoad.then((successMessage) => {
        const pageData = $this.listingsChunk;
        $this.propertyList = pageData[0];
      });
    }
    this.nextHighlight();
  },
  methods: {
    highlightMarker(mls) {
      const marker = document.getElementsByClassName("marker" + mls);
      const markers = document.getElementsByClassName("selected-marker");

      if (marker.length !== 0) {
        marker[0].classList.add("selected-marker");
      }

      for (let i = 0; i < markers.length; i++) {
        if (!markers[i].className.includes("marker" + mls)) {
          markers[i].classList.remove("selected-marker");
        }
      }
    },
    navPagination() {
      this.$store.commit("setNavigateFromMap", 1);
      this.$store.commit("setPageLoadedSt", 2);
    },
    highlightList(list) {
      this.activeHouse = "house" + list.mls;

      if (this.clicked === list.mls) {
        const routeData = this.$router.resolve({
          name: "home-state-mls",
          params: {
            state: list.state,
            mls: list.mls
          }
        });
        window.open(routeData.href, "_blank");
      }
      this.clicked = list.mls;
    },
    clearFilters($evnt) {
      this.$emit("clearFilters", "clear");
      window.location.reload();
    },
    showCurrentList() {
      return this.listingsChunk[this.currentPage];
    },
    nextHighlight() {
      const href = "#house" + this.selectedMls.mls;
      const el = document.querySelector(href);
      const mvCmd = this.$store.getters.getSelectedListing;
      if (mvCmd !== null) {
        if (el !== null) {
          this.$refs.listingContainer.scrollTop = el.offsetTop;
        }
      }
    }
  }
};
</script>
