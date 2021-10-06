<template>
  <b-col class="filter-bar-wrp">
    <b-row class="filterBar search-form">
      <b-col md="5" sm="12" class="pl-0 pr-0 d-flex align-items-center">
        <div class="w-auto">
          <h1 class="font-25 color-dark-green fw-600">Favorites &nbsp;</h1>
          <div id="TopOfTheListing" ref="TopOfTheListing">
            <a ref="linkTag" tabindex="0">a</a>
          </div>
        </div>
        <nuxt-link class="fw-500" to="/">Search all listings</nuxt-link>
      </b-col>
      <b-col
        v-if="!showSort"
        md="7"
        sm="12"
        class="text-left priceDropDown m-pl-0 m-pr-0"
      >
        <div class="dropdown minMaxDropDown">
          <div class="desktop-view fw-500 font-16">Sort: &nbsp;</div>
          <b-dropdown
            id="dropdown-1"
            :text="dropDownText"
            class="m-md-2 desktop-view sort-favorite"
          >
            <b-dropdown-item @click="sortChanged('rec')">
              Recommended
            </b-dropdown-item>
            <b-dropdown-item @click="sortChanged('a')">
              Favorite, newest to oldest
            </b-dropdown-item>
            <b-dropdown-item @click="sortChanged('b')">
              Favorite, oldest to newest
            </b-dropdown-item>
            <b-dropdown-item @click="sortChanged('c')">
              Listed, newest to oldest
            </b-dropdown-item>
            <b-dropdown-item @click="sortChanged('d')">
              Listed, oldest to newest
            </b-dropdown-item>
            <b-dropdown-item @click="sortChanged('e')">
              Price, low to high
            </b-dropdown-item>
            <b-dropdown-item @click="sortChanged('f')">
              Price, high to low
            </b-dropdown-item>
          </b-dropdown>

          <div class="form-select-container">
            <b-form-select
              v-model="selected"
              class="mb-3 mobile-view"
              @change="sortChanged()"
            >
              <b-form-select-option selected value="rec"
                >Recommended</b-form-select-option
              >
              <b-form-select-option value="a">
                Favorite, newest to oldest
              </b-form-select-option>
              <b-form-select-option value="b">
                Favorite, oldest to newest
              </b-form-select-option>
              <b-form-select-option value="c">
                Listed, newest to oldest
              </b-form-select-option>
              <b-form-select-option value="d">
                Listed, oldest to newest
              </b-form-select-option>
              <b-form-select-option value="e">
                Price, low to high
              </b-form-select-option>
              <b-form-select-option value="f">
                Price, high to low
              </b-form-select-option>
            </b-form-select>
            <span class="custom-appearance mobile-view">
              <img src="@/assets/icons/Down-arrow-small.svg" />
            </span>
          </div>
        </div>
        <!-- <span
          v-if="!filterOn"
          class="moreFilter position-static theme-font-light float-right"
          @click="filterchanged()"
        >
          More filters&nbsp;
          <img
            src="@/assets/icons/filter_arrow_down.svg"
            style="transform: rotate(0deg); transition: all .3s"
            class="mb-2"
            width="15px"
            height="15px"
          />
        </span>
        <span
          v-else
          class="moreFilter position-static theme-font-light float-right"
          @click="filterchanged()"
        >
          Less filters&nbsp;
          <img
            src="@/assets/icons/filter_arrow_down.svg"
            style="transform: rotate(180deg); transition: all .3s"
            width="15px"
            height="15px"
          />
        </span> -->
      </b-col>
    </b-row>
    <b-modal
      ref="locationError"
      hide-footer
      centered
      :title="'Sorry, we couldn\’t find (' + modalTitle + ').'"
    >
      <p class="my-2 font-15">
        Please check the spelling, try clearing the search box, or try
        reformatting to match these examples:
      </p>
      <div class="my-2 font-15">
        <ul class="list-group-flush">
          <li>Address: '123 Main St, Bellevue, WA'</li>
          <li>Zip: '98004'</li>
          <li>City: 'Bellevue' or 'Bellevue, WA'</li>
          <li>MLS number: '20109001'</li>
        </ul>
      </div>
    </b-modal>
  </b-col>
</template>
<script>
// $this.$refs["locationError"].show();
// import Axios from "axios";
// import SearchInputs from "@/components/SearchInputs";
import { Helper } from "@/plugins/mixins/helper";

export default {
  name: "FavFilterBar",
  components: {
    // SearchInputs
  },
  mixins: [Helper],
  props: {
    showSort: {
      type: Boolean
    }
  },
  data() {
    return {
      inputText: "",
      modalTitle: "",
      searchInput: "",
      submitText: "",
      anyPriceVal: null,
      inputType: "filterbar",
      searchButton: true,
      showDropDown: false,
      search: null,
      inSearchVal: "",
      mapLocation: null,
      selected: "rec",
      text: "",
      filterOn: false,
      priceLow: null,
      priceHigh: null,
      mapDetails: {},
      currInput: "",
      searchForm: {
        searchText: "",
        criteria: {
          price: {
            low: null,
            high: null
          }
        }
      },
      dropDownText: "Recommended",
      sortDropDown: {
        a: "Favorite, newest to oldest",
        b: "Favorite, oldest to newest",
        c: "Listed, newest to oldest",
        d: "Listed, oldest to newest",
        e: "Price, low to high",
        f: "Price, high to low",
        rec: "Recommended"
      },
      currentCenter: {
        lat: null,
        lng: null
      }
    };
  },
  computed: {
    updateMap() {
      return this.$store.getters.geoStatus;
    },
    favListing() {
      return this.$store.getters.getFavListingsAll;
    }
  },
  watch: {
    favListing() {
      if (this.selected) {
        this.sortingLists(this.favListing, this.selected);
      }
    }
  },
  mounted() {
    this.formattedAddress = this.$store.getters.getFormattedAddress;
    this.searchForm.searchText = this.$store.getters.getSearchInput;
    const searchHistory = localStorage.getItem("formattedAddress");
    if (searchHistory !== null) {
      this.inSearchVal = searchHistory;
    }
    // this.submitSearchForm(this.$store.getters.getSearchInput);
  },
  methods: {
    sortChanged(type) {
      if (type) {
        this.selected = type;
        this.sortingLists(this.favListing, type);
      } else {
        this.sortingLists(this.favListing, this.selected);
      }
    },
    storeText(val) {
      this.inputText = val;
    },
    sortingLists(data, type) {
      const dd = this.sortDropDown;
      const list = this.favListing;
      const newList = [];
      let i = 0;
      for (i = 0; i < list.length; i++) {
        newList.push(list[i]);
      }

      if (type === "e") {
        this.dropDownText = dd.e;
        newList.sort(function(a, b) {
          return a.listingPrice - b.listingPrice;
        });
        this.finalSearchedData(newList, null);
      }

      if (type === "f") {
        this.dropDownText = dd.f;
        newList.sort(function(a, b) {
          return b.listingPrice - a.listingPrice;
        });
        this.finalSearchedData(newList, null);
      }

      if (type === "c") {
        this.dropDownText = dd.c;
        newList.sort(function(a, b) {
          const aDate = new Date(a.daysOnMarket);
          const bDate = new Date(b.daysOnMarket);
          return aDate - bDate;
        });
        this.finalSearchedData(newList, null);
      }

      if (type === "d") {
        this.dropDownText = dd.d;
        newList.sort(function(a, b) {
          const aDate = new Date(a.daysOnMarket);
          const bDate = new Date(b.daysOnMarket);
          return bDate - aDate;
        });
        this.finalSearchedData(newList, null);
      }

      if (type === "a") {
        this.dropDownText = dd.a;
        newList.sort(function(a, b) {
          const aDate = new Date(a.createdAt);
          const bDate = new Date(b.createdAt);
          return aDate.getDate() - bDate.getDate();
        });
        this.finalSearchedData(newList, null);
      }

      if (type === "b") {
        this.dropDownText = dd.b;
        newList.sort(function(a, b) {
          const aDate = new Date(a.createdAt);
          const bDate = new Date(b.createdAt);
          return bDate.getDate() - aDate.getDate();
        });
        this.finalSearchedData(newList, null);
      }

      if (type === "rec") {
        this.dropDownText = dd.rec;
        this.finalSearchedData(list, null);
      }
    },
    finalSearchedData(res, sorting = null) {
      const data = res;
      const mlsList = [];
      const mapMarkerData = [];
      this.$store.commit("setTotalDataCount", data.length);
      for (let d = 0; d <= data.length; d++) {
        if (data[d] !== undefined) {
          const marker = {
            position: {
              lat: data[d].latitude,
              lng: data[d].longitude
            },
            info: {
              mls: data[d].mls,
              listingPrice: data[d].listingPrice,
              hasOpenHouse: data[d].hasOpenHouse,
              state: data[d].state,
              status: data[d].status
            }
          };

          mapMarkerData.push(marker);
          mlsList.push(data[d].mls);
        }
      }

      let i;
      let j;
      let pageData;
      let rightData;
      const tempList = [];
      const rightListingData = [];
      const chunk = 20;
      for (i = 0, j = mlsList.length; i < j; i += chunk) {
        pageData = mlsList.slice(i, i + chunk);
        rightData = data.slice(i, i + chunk);
        tempList.push(pageData);
        rightListingData.push(rightData);
      }

      this.$store.commit("setFavListingsChunk", rightListingData);
    }
  }
};
</script>
