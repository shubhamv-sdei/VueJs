<template>
  <b-col class="filter-bar-wrp">
    <b-row class="filterBar search-form">
      <b-col sm="12" md="12" lg="12" xl="5" class="pl-0 pr-0">
        <div class="input-group w-auto desktop-view">
          <h4 class="font-18 fw-regular mb-0">Real Estate & Homes for Sale</h4>
          <div class="font-12 fw-regular">
            Showing {{ numPerPage() }} of {{ totListing }} homes

            <b-dropdown
              id="dropdown-1"
              class="m-md-2 desktop-view sort-listings"
            >
              <template v-slot:button-content>
                <span
                  >&emsp;<img src="@/assets/icons/sorting.svg" /> sort
                </span>
              </template>
              <b-dropdown-item @click="sortChanged('rec')">
                Recommended
              </b-dropdown-item>
              <b-dropdown-item @click="sortChanged('e')">
                Price, low to high
              </b-dropdown-item>
              <b-dropdown-item @click="sortChanged('f')">
                Price, high to low
              </b-dropdown-item>
              <!-- <b-dropdown-item @click="sortChanged('a')">
                Favorite, newest to oldest
              </b-dropdown-item> -->
              <!-- <b-dropdown-item @click="sortChanged('b')">
                Favorite, oldest to newest
              </b-dropdown-item> -->
              <b-dropdown-item @click="sortChanged('c')">
                Listed, newest to oldest
              </b-dropdown-item>
              <b-dropdown-item @click="sortChanged('d')">
                Listed, oldest to newest
              </b-dropdown-item>
            </b-dropdown>
          </div>
        </div>
        <div class="input-group w-auto mobile-view">
          <SearchInputs
            class="ci"
            :show-location-icon="true"
            :show-search="true"
            :placeholder-text="inSearchVal"
            :submit-text="submitText"
            :input-type="inputType"
          />
        </div>
        <div class="input-group w-auto mobile-view m-responsive">
          <h4 class="font-18 fw-regular mb-0">Real Estate & Homes for Sale</h4>
          <div class="font-12 fw-regular">
            Showing {{ numPerPage() }} of {{ totListing }} homes

            <b-dropdown
              id="dropdown-1"
              class="m-md-2 desktop-view sort-listings"
            >
              <template v-slot:button-content>
                <span
                  >&emsp;<img src="@/assets/icons/sorting.svg" /> sort
                </span>
              </template>
              <b-dropdown-item @click="sortChanged('rec')">
                Recommended
              </b-dropdown-item>
              <b-dropdown-item @click="sortChanged('e')">
                Price, low to high
              </b-dropdown-item>
              <b-dropdown-item @click="sortChanged('f')">
                Price, high to low
              </b-dropdown-item>
              <!-- <b-dropdown-item @click="sortChanged('a')">
                Favorite, newest to oldest
              </b-dropdown-item> -->
              <!-- <b-dropdown-item @click="sortChanged('b')">
                Favorite, oldest to newest
              </b-dropdown-item> -->
              <b-dropdown-item @click="sortChanged('c')">
                Listed, newest to oldest
              </b-dropdown-item>
              <b-dropdown-item @click="sortChanged('d')">
                Listed, oldest to newest
              </b-dropdown-item>
            </b-dropdown>
          </div>
        </div>
        <div id="TopOfTheListing" ref="TopOfTheListing">
          <a ref="linkTag" tabindex="0">a</a>
        </div>
      </b-col>
      <b-col
        sm="12"
        md="12"
        lg="12"
        xl="7"
        class="text-left priceDropDown m-pl-0 m-pr-0"
      >
        <div class="dropdown minMaxDropDown">
          <button
            class="btn btn-primary dropbtn shadow-none theme-font-semi-bold"
            @click.prevent="showDropDown = !showDropDown"
          >
            <img src="~/assets/icons/filter_arrow_gray.svg" class="arrow" />
            {{ anyPriceVal }}
          </button>
          <div
            class="dropdown-content py-3 shadow-sm"
            :class="{ 'd-block': showDropDown }"
          >
            <b-col class="d-flex flex-column">
              <div class="priceInps" :class="priceLow ? 'hasValue' : ''">
                <input
                  id="minPriceTop"
                  v-model="priceLow"
                  class="form-control"
                  placeholder="Min"
                  name="min_price"
                  type="text"
                  @keyup="sepCommaA"
                />
                <div
                  v-if="priceLow"
                  class="clearInput"
                  @click="priceLow = null"
                >
                  &otimes;
                </div>
              </div>

              <p class="text-center mt-2 mb-2">to</p>
              <div
                class="priceInps"
                :class="priceHigh !== null ? 'hasValue' : ''"
              >
                <input
                  id="maxPriceTop"
                  v-model="priceHigh"
                  class="form-control"
                  placeholder="Max"
                  name="max_price"
                  type="text"
                  @keyup="sepCommaB"
                />
                <div
                  v-if="priceHigh"
                  class="clearInput"
                  @click="priceHigh = null"
                >
                  &otimes;
                </div>
              </div>
            </b-col>
            <b-col class="d-flex flex-column">
              <p class="text-right mt-3 mb-0">
                <a href="#" class="link-g" @click.prevent="priceRangeUpdated">
                  Done
                </a>
              </p>
            </b-col>
          </div>
        </div>
        <span
          v-if="!filterOn"
          class="moreFilter position-static theme-font-light float-right"
          @click="filterchanged()"
        >
          More filters
        </span>
        <span
          v-else
          class="moreFilter position-static theme-font-light float-right"
          @click="filterchanged()"
        >
          Less filters
        </span>
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
import Axios from "axios";
import SearchInputs from "@/components/SearchInputs";
import { Helper } from "@/plugins/mixins/helper";
import { EventBus } from "@/components/bus.js";

export default {
  name: "FilterBar",
  components: {
    SearchInputs
  },
  // mixins: [Helper],
  data() {
    return {
      inputText: "",
      modalTitle: "",
      searchInput: "",
      submitText: "",
      anyPriceVal: null,
      inputType: "filterbar",
      selected: "rec",
      searchButton: true,
      showDropDown: false,
      listPerPage: 20,
      search: null,
      inSearchVal: "",
      mapLocation: null,
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
      currentCenter: {
        lat: null,
        lng: null
      },
      sortDropDown: {
        a: "Favorite, newest to oldest",
        b: "Favorite, oldest to newest",
        c: "Listed, newest to oldest",
        d: "Listed, oldest to newest",
        e: "Price, low to high",
        f: "Price, high to low",
        rec: "Recommended"
      }
    };
  },
  computed: {
    updateMap() {
      return this.$store.getters.geoStatus;
    },
    filterSubmitted() {
      return this.$store.getters.gethideFilter;
    },
    checkPriceCleared() {
      return this.$store.getters.getPriceFiltered;
    },
    totListing() {
      return this.$store.getters.getTotalDataCount;
    },
    chuckNum() {
      return this.$store.getters.getListingsChunckCount;
    },
    listings() {
      return this.$store.getters.getListings;
    }
  },
  watch: {
    filterSubmitted(d) {
      this.filterOn = d;
      // if (this.filterOn === true) {
      //   this.filterOn = false;
      // }
    },
    checkPriceCleared(d) {
      if (d) {
        this.priceHigh = null;
        this.priceLow = null;
        this.anyPriceVal = "Any price range";
      }
    },
    chuckNum(nm) {
      this.listPerPage = nm;
    }
  },
  mounted() {
    this.formattedAddress = this.$store.getters.getFormattedAddress;
    this.searchForm.searchText = this.$store.getters.getSearchInput;
    const searchHistory = localStorage.getItem("formattedAddress");
    if (searchHistory !== null) {
      this.inSearchVal = searchHistory;
    }
    this.addAnyPriceVal(true);
    // this.submitSearchForm(this.$store.getters.getSearchInput);
  },
  methods: {
    emitPriceChangeClickEvent() {
      EventBus.$emit("i-got-clicked", "extra data");
    },
    storeText(val) {
      this.inputText = val;
    },
    numPerPage() {
      return this.totListing < this.listPerPage
        ? this.totListing
        : this.listPerPage;
    },
    addAnyPriceVal(load = false) {
      const numSaved = localStorage.getItem("filteredData");
      let prSavedLow = null;
      let prSavedHigh = null;
      if (numSaved !== null) {
        const numObj = JSON.parse(numSaved);
        if (numObj.criteria !== undefined) {
          if (numObj.criteria.price !== undefined) {
            prSavedLow = numObj.criteria.price.low || null;
            prSavedHigh = numObj.criteria.price.high || null;
          }
        }
      }
      const low = load
        ? Helper.methods.priceConv(prSavedLow)
        : Helper.methods.priceConv(this.priceLow);

      const high = load
        ? Helper.methods.priceConv(prSavedHigh)
        : Helper.methods.priceConv(this.priceHigh);

      if (low && high) {
        this.anyPriceVal = `${low} to ${high}`;
      } else if (low === null && high) {
        this.anyPriceVal = `Up to ${high}`;
      } else if (low && high === null) {
        this.anyPriceVal = `${low}+`;
      } else {
        this.anyPriceVal = `Any price range`;
      }
    },
    filterchanged() {
      this.filterOn = !this.filterOn;
      const searchTxt = "";
      // if(this.$store.getters.getSearchInput !== undefined &&  this.$store.getters.getSearchInput.length > 0)
      // {
      //     searchTxt =this.$store.getters.getSearchInput;
      // }
      // searchTxt= this.inputText.target.value;
      // console.log("text is : " + this.$store.getters.getSearchInput);
      this.$store.commit("sethideFilter", this.filterOn);
      this.$emit("filter-visible-changed", searchTxt);
    },
    sepCommaA() {
      if (this.priceLow) {
        this.priceLow = this.priceLow.replace(/,/g, "");
        this.priceLow = this.priceLow
          .toString()
          .replace(/\B(?=(\d{3})+(?!\d))/g, ",");
      }
    },
    sepCommaB() {
      if (this.priceHigh) {
        this.priceHigh = this.priceHigh.replace(/,/g, "");
        this.priceHigh = this.priceHigh
          .toString()
          .replace(/\B(?=(\d{3})+(?!\d))/g, ",");
      }
    },
    filterObject(form) {
      const beds = `"beds": {
          "low": 0,
          "high": 0
        },`;
      const bath = `"baths": {
          "low": 0,
          "high": 0
        },`;
      const price = `"price": {
          "low": 0,
          "high": 0
        },`;
      const yearBuilt = `"year_built": {
          "low": 0,
          "high": 0
        },`;
      const squareFeet = `"square_feet": {
          "low": 0,
          "high": 0
        },`;
      const homeOwnerDue = `"homeowner_due": {
          "low": 0,
          "high": 0
        }`;
      const listingTypes = `"listingTypes": ["VACL"],`;
      const listingStatus = `"listingStatuses": ["None"],`;
      const criteria = `"criteria": {
        ${form.beds ? beds : ""}
        ${form.bath ? bath : ""}
        ${form.price ? price : ""}
        ${form.yearBuilt ? yearBuilt : ""}
        ${form.squareFeet ? squareFeet : ""}
        ${form.homeOwnerDue ? homeOwnerDue : ""}
        ${form.listingTypes ? listingTypes : ""}
        ${form.listingStatus ? listingStatus : ""}
      }`;
      const data = `{
      "latitude": ${form.lat},
      "longitude": ${form.long},
      "searchRadius": 15,
      ${form.criteria ? criteria : ""}
    }`;
      return data;
    },
    submitSearchForm(text = "") {
      const This = this;
      // const searchedText = text.toLowerCase();
      // console.log(this.$store.getters.getGeometry.lat);
      let selected;
      if (this.$store.getters.getSearchFrom === "buy") {
        selected = {
          latitude: this.$store.getters.getGeometry.lat,
          longitude: this.$store.getters.getGeometry.lng,
          searchRadius: 2500
        };
      } else {
        selected = this.filterObject(This.searchForm);
      }

      const Url = this.$apiPath("main", "Listings/Search", "");
      Axios({
        method: "POST",
        url: Url,
        data: selected,
        headers: {
          "Content-Type": "application/json",
          "X-Client-Token": This.$store.state.X_CLIENT_TOKEN
        }
      }).then(
        (result) => {
          console.info(result);
          //   This.search = null;
          if (result.data.statusCode === "Success") {
            This.$emit("search-fired", result.data.data);
            This.$store.commit("setHouseList", result.data.data);
            // This.$emit('setListing', result.data.data);
          }
        },
        (error) => {
          throw new Error(error);
        }
      );
    },
    getAddress(place) {
      const vp = JSON.stringify(place.geometry.viewport);
      const lc = JSON.stringify(place.geometry.location);
      const mapDetails = {
        location: JSON.parse(lc),
        viewport: JSON.parse(vp)
      };
      this.mapDetails = mapDetails;

      localStorage.setItem("mapDetails", JSON.stringify(mapDetails));
      this.$store.commit("setApiLoadingStatus", true);
      if (place.types !== undefined) {
        this.cityGeometry = place.geometry.viewport;
        const types = place.types;
        // localStorage.setItem("formattedAddress", place.formatted_address);
        if (types.includes("premise")) {
          this.searchedVal = place.formatted_address;
          this.searchByText(this.searchedVal);
        } else if (types.includes("street_address")) {
          this.searchedVal = place.formatted_address;
          this.searchByText(this.searchedVal);
          this.$store.commit("setFormattedAddress", this.searchedVal);
          this.inSearchVal = this.searchedVal;
          localStorage.setItem("formattedAddress", this.searchedVal);
        } else if (types.includes("locality") || types.includes("political")) {
          // this.getLocationCenter(place.formatted_address);
          const Geometry = {
            lat: place.geometry.viewport.Ya.i,
            lng: place.geometry.viewport.Ua.i
          };
          this.$store.commit("setGeometry", Geometry);
          localStorage.setItem("geometry", JSON.stringify(Geometry));

          this.searchedVal = place.formatted_address;
          localStorage.setItem("formattedAddress", this.searchedVal);
          this.$store.commit("setFormattedAddress", this.searchedVal);
          this.inSearchVal = this.searchedVal;
          this.searchByText(this.searchedVal);
        } else if (types.includes("postal_code")) {
          this.searchedVal = place.address_components[0].short_name;
          this.$store.commit("setFormattedAddress", place.formatted_address);
          this.searchByText(place.address_components[0].short_name);
          localStorage.setItem("formattedAddress", this.searchedVal);
          this.inSearchVal = this.searchedVal;
          this.searchByText(this.searchedVal);
        } else {
          this.text = place.formatted_address.toLowerCase();
          this.$store.commit("setSearchInput", this.text);
          this.$store.commit("setGeoStatus", true);
          this.$store.commit("setFormattedAddress", place.formatted_address);

          this.$store.commit("setGeometry", {
            lat: place.geometry.location.lat(),
            lng: place.geometry.location.lng()
          });
          const Geometry = {
            lat: place.geometry.location.lat(),
            lng: place.geometry.location.lng()
          };
          localStorage.setItem("geometry", JSON.stringify(Geometry));

          this.$store.commit("setSearchFrom", "buy");
          this.$store.commit("setSearchInput", this.text);

          if (this.$route.path === "/sell") {
            this.showEnterNumber = true;
            this.messageText = "";
            this.$bvModal.show("getPhoneNumber");
          } else {
            this.modalTitle = place.formatted_address;
            this.$refs.locationError.show();
          }
        }
      }
    },
    checkInputValue($evt) {
      if ($evt.target.value === "") {
        this.searchButton = true;
      } else {
        this.searchInput = $evt.target.value;
        this.searchButton = false;
      }
    },
    searchByText(selected) {
      this.$store.commit("setApiLoadingStatus", true);
      const input = this.inputText;

      const $this = this;
      const Url = this.$apiPath("main", "Listings/Search", "");
      let inpEl;
      if (input.target !== undefined) {
        inpEl = input.target.value;
      }

      const inpVal = inpEl || input;
      if (inpVal) {
        this.modalTitle = input.target.value;
      } else {
        this.modalTitle = inpVal;
      }

      localStorage.setItem("formattedAddress", selected);
      this.inSearchVal = selected;
      const searchData = {
        searchText: selected
      };

      Axios({
        method: "POST",
        url: Url,
        data: searchData,
        headers: {
          "Content-Type": "application/json",
          "X-Client-Token": $this.$store.state.X_CLIENT_TOKEN
        }
      }).then(
        (result) => {
          if (result.data.statusCode === "Success") {
            if (result.data.data.length === 0) {
              $this.$refs.locationError.show();
            } else {
              localStorage.setItem(
                "searchedCity",
                result.data.data[0].city + ", " + result.data.data[0].state
              );
              if (result.data.data.length === 1) {
                const state = result.data.data[0].state;
                const mls = result.data.data[0].mls;
                $this.$router.push({
                  name: "home-state-mls",
                  params: { state, mls }
                });
              } else if (result.data.data.length > 1) {
                $this.$store.commit("setHouseList", result.data.data);
                const mapGeo = {
                  lat: result.data.data[0].latitude,
                  lng: result.data.data[0].longitude
                };
                this.$store.commit("setGeometry", mapGeo);
                this.$emit("on-submit", this.text);
                localStorage.setItem("geometry", JSON.stringify(mapGeo));
                // location.reload();
              } else {
                $this.$refs.locationError.show();
              }
            }
          } else {
            $this.$refs.locationError.show();
          }
        },
        (error) => {
          throw new Error(error);
        }
      );
    },
    async searchByClick() {
      this.$store.commit("setApiLoadingStatus", true);
      const input = this.inputText;
      const $this = this;
      setTimeout(() => {
        $this.$refs.linkTag.focus();
      }, 300);
      const Url = this.$apiPath("main", "Listings/Search", "");
      this.modalTitle = input.target.value;
      const searchData = {
        searchText: input.target.value
      };
      localStorage.setItem("formattedAddress", input.target.value);
      await Axios({
        method: "POST",
        url: Url,
        data: searchData,
        headers: {
          "Content-Type": "application/json",
          "X-Client-Token": $this.$store.state.X_CLIENT_TOKEN
        }
      }).then(
        (result) => {
          console.info(result);
          if (result.data.statusCode === "Success") {
            if (result.data.data.length === 0) {
              $this.checkAddress();
            } else {
              if (result.data.data.length === 1) {
                const state = result.data.data[0].state;
                const mls = result.data.data[0].mls;
                $this.$router.push({
                  name: "home-state-mls",
                  params: { state, mls }
                });
              } else if (result.data.data.length > 1) {
                const mapGeo = {
                  lat: result.data.data[0].latitude,
                  lng: result.data.data[0].longitude
                };
                const mainObj = {
                  location: {
                    lat: result.data.data[0].latitude,
                    lng: result.data.data[0].longitude
                  }
                };
                const mainObjToStr = JSON.stringify(mainObj);
                localStorage.setItem("mapDetails", mainObjToStr);

                $this.$store.commit("setHouseList", result.data.data);
                localStorage.setItem("geometry", JSON.stringify(mapGeo));
                $this.apiLoading = false;
              }

              return false;
            }
          }
        },
        (error) => {
          console.error(error);
        }
      );
    },
    async searchFromFilterApi() {
      this.apiLoading = true;
      const input = this.inputText;

      const $this = this;
      const Url = this.$apiPath("main", "Listings/Filter", "");
      this.modalTitle = input.target.value;
      const searchData = {
        latitude: this.mapDetails.location.lat,
        longitude: this.mapDetails.location.lng,
        searchRadius: 5000
      };
      localStorage.setItem("formattedAddress", input.target.value);

      await Axios({
        method: "POST",
        url: Url,
        data: searchData,
        headers: {
          "Content-Type": "application/json",
          "X-Client-Token": $this.$store.state.X_CLIENT_TOKEN
        }
      }).then(
        (result) => {
          if (result.data.statusCode === "Success") {
            if (result.data.data !== null) {
              if (result.data.data.length === 0) {
                $this.$refs.locationError.show();
                $this.apiLoading = false;
              } else {
                if (result.data.data.length === 1) {
                  const state = result.data.data[0].state;
                  const mls = result.data.data[0].mls;
                  $this.$router.push({
                    name: "home-state-mls",
                    params: { state, mls }
                  });
                } else if (result.data.data.length > 1) {
                  const mapGeo = {
                    lat: $this.mapDetails.location.lat,
                    lng: $this.mapDetails.location.lng
                  };
                  $this.$store.commit("setHouseList", result.data.data);
                  localStorage.setItem("geometry", JSON.stringify(mapGeo));
                }

                return false;
              }
            } else {
              $this.$refs.locationError.show();
              $this.apiLoading = false;
            }
          }
        },
        (error) => {
          console.error(error);
        }
      );
    },
    async checkAddress() {
      const $this = this;
      const gApi = "https://maps.googleapis.com/maps/api/geocode/json";
      const input = this.inputText;
      const searchTxt = input.target.value;
      this.modalTitle = searchTxt;
      const mainPoint = localStorage.getItem("mapDetails");
      const readyTxt = searchTxt.split(" ").join("+");
      const key = process.env.VUE_APP_GOOGLE_MAPS_API_KEY;
      const data = `${gApi}?address=${readyTxt}&key=${key}`;

      await Axios({
        method: "GET",
        url: data
      }).then(
        (result) => {
          if (result.data.results.length !== 0) {
            const addlocation = result.data.results[0].geometry.location;
            $this.mapDetails.location = addlocation;
            if (mainPoint !== null) {
              const mainObj = JSON.parse(mainPoint);
              mainObj.location = addlocation;
              const mainObjToStr = JSON.stringify(mainObj);
              localStorage.setItem("mapDetails", mainObjToStr);
            }
            $this.searchFromFilterApi();
          } else {
            $this.$refs.locationError.show();
            $this.apiLoading = false;
          }
        },
        (error) => {
          console.error(error);
        }
      );
    },
    priceRangeUpdated() {
      const mapInfo = this.$store.getters.getMapInfo;
      const searchedAddress = localStorage.getItem("formattedAddress");
      const $this = this;
      $this.showDropDown = false;
      const filterObj = {};

      const filterHistory = localStorage.getItem("filteredData");

      if (filterHistory !== null) {
        const jsonFtr = JSON.parse(filterHistory);
        if (jsonFtr.criteria) {
          filterObj.criteria = jsonFtr.criteria;
        }
      }
      const priceLow = this.priceLow
        ? Helper.methods.removeComma(this.priceLow)
        : "";
      const priceHigh = this.priceHigh
        ? Helper.methods.removeComma(this.priceHigh)
        : "";

      if (searchedAddress !== null) {
        filterObj.searchText = searchedAddress;
      }

      if (priceLow) {
        filterObj.criteria = {};
        filterObj.criteria.price = {};
        filterObj.criteria.price.low = priceLow;
      }
      if (priceHigh) {
        filterObj.criteria = {};
        filterObj.criteria.price = {};
        if (priceLow) {
          filterObj.criteria.price.low = priceLow;
        }
        filterObj.criteria.price.high = priceHigh;
      }

      filterObj.latitude = mapInfo.location.lat;
      filterObj.longitude = mapInfo.location.lng;
      filterObj.searchRadius = mapInfo.zoom;

      const priceData = filterObj;
      localStorage.setItem("filteredData", JSON.stringify(priceData));

      this.$store.commit("setPriceFiltered", false);
      const Url = this.$apiPath("main", "Listings/Filter", "");
      Axios({
        method: "POST",
        url: Url,
        data: priceData,
        headers: {
          "Content-Type": "application/json",
          "X-Client-Token": $this.$store.state.X_CLIENT_TOKEN
        }
      }).then(
        (result) => {
          $this.addAnyPriceVal();
          if (result.data.statusCode === "Success") {
            $this.$emit("search-fired", false);
            // $this.$store.commit("setHouseList", result.data.data);
            $this.finalSearchedData(result.data.data, false);
          }
        },
        (error) => {
          throw new Error(error);
        }
      );
      this.emitPriceChangeClickEvent();
    },
    finalSearchedData(res, navigate = true) {
      const data = res;
      const mlsList = [];
      const mapMarkerData = [];
      this.$store.commit("setHouseList", data);
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

      this.$store.commit("setMapMarketData", mapMarkerData);

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
      this.$store.commit("setListingsMls", tempList);
      this.$store.commit("setListingsChunk", rightListingData);
      if (navigate) {
        this.$router.push("listings");
      }
    },
    sortChanged(type) {
      if (type) {
        this.selected = type;
        this.sortingLists(this.listings, type);
      } else {
        this.sortingLists(this.listings, this.selected);
      }
    },
    sortingLists(data, type) {
      const dd = this.sortDropDown;
      const list = this.listings;
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
    }
  }
};
</script>
<style type="text/css">
@media (max-width: 768px) {
  .input-group.w-auto.mobile-view.m-responsive {
    display: none;
  }
}
@media (max-width: 425px) {
  .input-group.w-auto.mobile-view {
    bottom: 18px;
  }
  .input-group.w-auto.mobile-view.m-responsive {
    box-shadow: none;
    top: 5px;
    display: block;
  }
  .filterBar .moreFilter {
    width: 190px !important;
  }
  .filterBar .minMaxDropDown button {
    min-width: 190px !important;
  }
}
@media (max-width: 375px) {
  .filterBar .minMaxDropDown button {
    min-width: 165px !important;
  }
  .filterBar .moreFilter {
    width: 165px !important;
  }
  .filter-bar-wrp .dropbtn {
    padding-right: 47px;
  }
}
.btn {
  padding: 10px 10px;
}
label#example-datepicker__value_ {
  box-shadow: none;
}
</style>
