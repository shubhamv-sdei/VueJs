<template>
  <div class="customTextinput-wrapper">
    <form
      class="getStartedPhoneCheck"
      @submit.prevent="$emit('on-submit', searchText)"
    >
      <div v-if="inputTypeCheck('filterbar')">
        <div class="locationIcon"></div>
        <gmap-autocomplete
          ref="inputRef"
          class="form-control"
          :placeholder="placeholderText"
          @input="storeText"
          @listeners="checkGinputVal($event)"
          @keyup="checkInputValue($event)"
          @keyup.enter="searchByClick()"
          @place_changed="getAddress"
          @change="checkInputValue"
        ></gmap-autocomplete>
        <button
          class="input-group-append filterSearchBtn"
          :disabled="searchButton"
          @click="searchByClick()"
        ></button>
      </div>
      <gmap-autocomplete
        v-if="inputTypeCheck('gmapAuto')"
        class="form-control searchInput inpB"
        :placeholder="placeholderText"
        @input="storeText"
        @listeners="checkGinputVal($event)"
        @keyup="checkInputValue($event)"
        @place_changed="getAddress"
        @change="checkInputValue"
      >
      </gmap-autocomplete>

      <div
        v-if="inputTypeCheck('gmapAuto')"
        class="submit"
        :style="showSearch ? 'max-width:68px;' : 'max-width:150px;'"
        align-v="center"
      >
        <button
          v-if="showSearch"
          type="submit"
          class="searchImg"
          :class="apiLoading ? 'isLoading' : ''"
          :disabled="searchButton"
          @click="searchByClick()"
          @focus="checkGinputVal('focussed')"
        ></button>
      </div>

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
    </form>
  </div>
</template>

<script>
import Axios from "axios";
import { Helper } from "@/plugins/mixins/helper";

export default {
  name: "SearchInputs",
  mixins: [Helper],
  props: {
    placeholderText: {
      type: String,
      default: ""
    },
    showLocationIcon: Boolean,
    showDollar: Boolean,
    showGmapAuto: Boolean,
    submitText: {
      type: String,
      default: ""
    },
    showSearch: Boolean,
    inputType: {
      type: String,
      default: ""
    },
    loading: Boolean
  },
  data() {
    return {
      text: "",
      searchText: "",
      appPhone: "",
      inputElement: "",
      inSearchVal: "",
      modalTitle: "",
      letterCount: 0,
      countryCode: 1,
      countryCodeStr: "1",
      searchButton: true,
      apiLoading: false,
      showEnterNumber: false,
      mapDetails: {},
      flag: "us",
      searchInput: "",
      messageText: "We’ll text you an app download link!",
      cityGeometry: {},
      searchedVal: ""
    };
  },
  computed: {
    // inputstyle() {
    //   if (this.showLocationIcon || this.showCountry) {
    //     if (this.showLocationIcon) {
    //       return "padding-left: 50px;";
    //     }

    //     return "padding-left: 70px;";
    //   }
    //   return "";
    // },
    submitButtonSt() {
      return this.$store.getters.getLoadingBcomeAgentBtn;
    }
  },
  watch: {
    $route() {
      this.searchButton = false;
    },
    submitButtonSt(val) {
      this.searchButton = val;
    }
  },
  mounted() {
    this.formattedAddress = this.$store.getters.getFormattedAddress;
    this.searchText = this.$store.getters.getSearchInput;
    const searchHistory = localStorage.getItem("formattedAddress");
    if (searchHistory !== null) {
      this.inSearchVal = searchHistory;
    }
    // this.$store.commit("setLoadingBcomeAgentBtn", false);
  },
  methods: {
    storeText(val) {
      this.inputElement = val;
    },
    isNumber(evt) {
      // console.log(evt);
      evt = evt || window.event;
      const charCode = evt.which ? evt.which : evt.keyCode;
      if (
        charCode > 31 &&
        (charCode < 48 || charCode > 57) &&
        charCode !== 46
      ) {
        evt.preventDefault();
        this.props.placeholderText = "Accept only number";
      } else {
        return true;
      }
    },
    inputTypeCheck(Num) {
      return Num === this.inputType;
    },
    getAddress(place) {
      this.$store.commit("setApiLoadingStatus", true);
      this.$store.commit("setTopSearchSt", true);
      if (place.geometry === undefined) {
        localStorage.setItem("searchedCity", place.name);
        return false;
      }
      const lc = JSON.stringify(place.geometry.location);
      const mapDetails = {
        location: JSON.parse(lc)
      };
      this.mapDetails = mapDetails;

      this.$store.commit("setGeometry", mapDetails);
      localStorage.setItem("geometry", JSON.stringify(mapDetails));
      localStorage.setItem("formattedAddress", place.formatted_address);
      this.apiLoading = true;

      this.$store.commit("setSearchFierd", true);
      if (this.$route.path === "/sell") {
        this.showEnterNumber = true;
        this.messageText = "";
        this.$bvModal.show("getPhoneNumber");
        this.apiLoading = false;
      } else if (place.types !== undefined) {
        this.cityGeometry = place.geometry.viewport;
        const types = place.types;
        // localStorage.setItem("formattedAddress", place.formatted_address);
        if (types.includes("premise")) {
          this.searchByText(place.formatted_address);
        } else if (types.includes("street_address")) {
          this.searchByText(place.formatted_address);
          this.$store.commit("setFormattedAddress", place.formatted_address);
          localStorage.setItem("formattedAddress", place.formatted_address);
        } else if (types.includes("locality") || types.includes("political")) {
          // this.getLocationCenter(place.formatted_address);
          const Geometry = {
            location: {
              lat: mapDetails.location.lat,
              lng: mapDetails.location.lng
            }
          };
          this.$store.commit("setGeometry", Geometry);
          localStorage.setItem("geometry", JSON.stringify(Geometry));
          this.searchedVal = place.formatted_address;
          this.$store.commit("setFormattedAddress", place.formatted_address);
          localStorage.setItem("formattedAddress", place.formatted_address);
          this.searchByText(this.searchedVal);
        } else if (types.includes("postal_code")) {
          this.searchedVal = place.address_components[0].short_name;
          this.$store.commit("setFormattedAddress", this.searchedVal);
          localStorage.setItem("formattedAddress", this.searchedVal);
          this.searchByText(this.searchedVal);
        } else {
          this.text = place.formatted_address.toLowerCase();
          this.$store.commit("setSearchInput", this.text);
          this.$store.commit("setGeoStatus", true);
          this.$store.commit("setFormattedAddress", place.formatted_address);

          const Geometry = {
            location: {
              lat: mapDetails.location.lat,
              lng: mapDetails.location.lng
            }
          };
          this.$store.commit("setGeometry", Geometry);
          localStorage.setItem("geometry", JSON.stringify(Geometry));

          this.$store.commit("setSearchFrom", "buy");
          this.$store.commit("setSearchInput", this.text);

          if (this.$route.path === "/sell") {
            this.showEnterNumber = true;
            this.messageText = "";
            this.$bvModal.show("getPhoneNumber");
          } else {
            // this.$router.push("listings");
            this.searchFromFilterApi();
            // this.modalTitle = place.formatted_address;
            // this.$refs.locationError.show();
          }
        }
      }
    },
    async searchByText(selected) {
      const input = this.inputElement;
      const filterObj = {};
      const filterHistory = localStorage.getItem("filteredData");
      if (filterHistory !== null) {
        const jsonFtr = JSON.parse(filterHistory);
        if (jsonFtr.criteria) {
          filterObj.criteria = jsonFtr.criteria;
        }
      }

      // localStorage.setItem("filteredData", JSON.stringify(filterObj));

      const $this = this;
      // const Url = this.$apiPath("main", "Listings/Search", "");
      const Url = this.$apiPath("main", "Listings/SearchWithBoundary", "");
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

      const searchData = {};
      searchData.searchText = selected;
      if (filterObj.criteria) {
        searchData.criteria = filterObj.criteria;
      }

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
          console.log(result.data.data.listings);
          console.log('I have added New Number');
          //   This.search = null;
          if (result.data.statusCode === "Success") {
            // if (result.data.data.length === 0) {
            //   $this.searchFromFilterApi();
            // } else {
            //   if (result.data.data.length === 1) {
            //     const state = result.data.data[0].state;
            //     const mls = result.data.data[0].mls;
            //     $this.$router.push({
            //       name: "home-state-mls",
            //       params: { state, mls }
            //     });
            //   } else if (result.data.data.length > 1) {
            //     // this.$emit("on-submit", this.text);
            //     const mapGeo = {
            //       location: {
            //         lat: $this.mapDetails.location.lat,
            //         lng: $this.mapDetails.location.lng
            //       }
            //     };
            //     localStorage.setItem("geometry", JSON.stringify(mapGeo));
            //     // $this.$store.commit("setHouseList", result.data.data);
            //     $this.$store.commit("setCityGeometry", $this.cityGeometry);
            //     $this.finalSearchedData(result.data.data);
            //   } else {
            //     $this.$refs.locationError.show();
            //     $this.apiLoading = false;
            //   }
            //   return false;
            // }

            if (result.data.data.listings.length === 0) {
              $this.searchFromFilterApi();
            } else {
              if (result.data.data.listings.length === 1) {
                const state = result.data.data.listings[0].state;
                const mls = result.data.data.listings[0].mls;
                $this.$router.push({
                  name: "home-state-mls",
                  params: { state, mls }
                });
              } else if (result.data.data.listings.length > 1) {
                // this.$emit("on-submit", this.text);
                const mapGeo = {
                  location: {
                    lat: $this.mapDetails.location.lat,
                    lng: $this.mapDetails.location.lng
                  }
                };
                console.log(mapGeo);
                console.log('INFO');
                localStorage.setItem("geometry", JSON.stringify(mapGeo));

                localStorage.setItem("boundary", JSON.stringify(result.data.data.boundary));
                // $this.$store.commit("setHouseList", result.data.data);
                $this.$store.commit("setCityGeometry", $this.cityGeometry);
                $this.finalSearchedData(result.data.data.listings);
              } else {
                $this.$refs.locationError.show();
                $this.apiLoading = false;
              }
              return false;
            }

            // $this.$emit("search-fired", result.data.data);
            // $this.$store.commit("setHouseList", result.data.data);
          } else {
            $this.$refs.locationError.show();
          }
        },
        (error) => {
          console.error(error);
        }
      );
    },
    async searchByClick() {
      const filterObj = {};
      const filterHistory = localStorage.getItem("filteredData");
      if (filterHistory !== null) {
        const jsonFtr = JSON.parse(filterHistory);
        if (jsonFtr.criteria) {
          filterObj.criteria = jsonFtr.criteria;
        }
      }

      this.apiLoading = true;
      this.$store.commit("setSearchFierd", true);
      this.$store.commit("setTopSearchSt", true);
      const input = this.inputElement;
      const $this = this;
      const Url = this.$apiPath("main", "Listings/Search", "");
      this.modalTitle = input.target.value;
      const searchData = {
        searchText: input.target.value
      };
      if (filterObj.criteria) {
        searchData.criteria = filterObj.criteria;
      }
      // localStorage.setItem("filteredData", JSON.stringify(filterObj));
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
                const mainObj = {
                  location: {
                    lat: result.data.data[0].latitude,
                    lng: result.data.data[0].longitude
                  }
                };
                const mainObjToStr = JSON.stringify(mainObj);
                localStorage.setItem("geometry", mainObjToStr);

                $this.finalSearchedData(result.data.data);
                // $this.$store.commit("setHouseList", result.data.data);
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
      const input = this.inputElement;
      this.$store.commit("setTopSearchSt", true);
      const filterObj = {};
      const filterHistory = localStorage.getItem("filteredData");
      if (filterHistory !== null) {
        const jsonFtr = JSON.parse(filterHistory);
        if (jsonFtr.criteria) {
          filterObj.criteria = jsonFtr.criteria;
        }
      }

      const $this = this;
      const Url = this.$apiPath("main", "Listings/Filter", "");
      this.modalTitle = input.target.value;
      const searchData = {
        latitude: this.mapDetails.location.lat,
        longitude: this.mapDetails.location.lng,
        searchRadius: 5000
      };
      if (filterObj.criteria) {
        searchData.criteria = filterObj.criteria;
      }
      // localStorage.setItem("filteredData", JSON.stringify(searchData));
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
                    location: {
                      lat: $this.mapDetails.location.lat,
                      lng: $this.mapDetails.location.lng
                    }
                  };
                  // $this.$store.commit("setHouseList", result.data.data);
                  $this.finalSearchedData(result.data.data);
                  // console.log('Lifgdfd');
                  // localStorage.removeItem("filteredData");
                  $this.$router.push("listings");
                  window.location.href = '/listings';
                  // window.open('/listings');
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
      const input = this.inputElement;
      const searchTxt = input.target.value;
      this.modalTitle = searchTxt;
      const mainPoint = localStorage.getItem("geometry");
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
              localStorage.setItem("geometry", mainObjToStr);
            } else {
              const lcDetails = {
                location: addlocation
              };

              localStorage.setItem("geometry", JSON.stringify(lcDetails));
            }
            const addr = result.data.results[0].formatted_address;
            localStorage.setItem("formattedAddress", addr);
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
    getLocationCenter(searchedLocation) {
      // window.google

      const geocoder = new window.google.maps.Geocoder();

      geocoder.geocode({ address: searchedLocation }, function(
        results,
        status
      ) {
        if (status === "OK") {
          const mapBounds = results[0].geometry.bounds;
          localStorage.setItem("geometryBounds", JSON.stringify(mapBounds));
          // map.setCenter(results[0].geometry.location);
        } else {
          alert(
            "Geocode was not successful for the following reason: " + status
          );
        }
      });
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
      this.apiLoading = false;
    },
    checkInputValue($evt) {
      this.letterCount = this.letterCount + 1;
      // if ($evt.target.value.length === 0) {
      //   this.letterCount = 0;
      // }
      // if ($evt.target.value.length === this.letterCount) {
      //   if ($evt.target.value.length === 0) {
      //     this.searchButton = true;
      //   } else {
      //     this.searchInput = $evt.target.value;
      //     this.searchButton = false;
      //   }
      // } else {
      //   this.searchButton = true;
      // }
      if ($evt.target.value === "") {
        this.searchButton = true;
      } else {
        this.searchInput = $evt.target.value;
        this.searchButton = false;
      }
    },
    checkGinputVal($evt) {
      console.log($evt);
    }
    /*
    checkInputValue($evt) {
      if ($evt.target.value === "") {
        this.searchButton = true;
      } else {
        this.searchInput = $evt.target.value;
        this.searchButton = false;
      }
    },
    checkGinputVal($evt) {
      console.log($evt);
    },
    addBracket(e) {
      const phone = e.target;
      if (phone.value.length === 0) {
        phone.value = "(" + phone.value;
      }
      this.checkInputValue(e);
    },
    showPhoneModal() {
      if (this.$store.getters.getLoadingBcomeAgentBtn) {
        this.searchButton = true;
      } else {
        this.searchButton = false;
      }
      this.messageText = "";
      if (this.$route.path === "/sell") {
        this.$bvModal.show("getPhoneNumber");
      }
    },
    submitAppRequest() {
      const This = this;
      this.messageText =
        "You will receive a message shortly with link to download the nexme app.";
      setTimeout(function() {
        This.$bvModal.hide("getPhoneNumber");
      }, 2000);
    },
    searchByText() {
      const input = this.inputElement;
      const $this = this;
      const Url = this.$apiPath("main", "Listings/Search", "");
      const searchData = {
        searchText: this.searchInput ? this.searchInput : input.target.value
      };
      this.modalTitle = input.target.value;

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
          console.info(result);
          //   This.search = null;
          if (result.data.statusCode === "Success") {
            console.log(result.data.data.length);
            if (result.data.data.length === 0) {
              $this.$refs.locationError.show();
            } else if (result.data.data.length === 1) {
              const state = result.data.data[0].state;
              const mls = result.data.data[0].mls;
              $this.$router.push({ name: "home", params: { state, mls } });
            } else if (result.data.data.length > 1) {
              // this.$emit("on-submit", this.text);
              const mapGeo = {
                lat: result.data.data[0].latitude,
                lng: result.data.data[0].longitude
              };
              localStorage.setItem("geometry", JSON.stringify(mapGeo));
              this.$router.push("listings");
            }

            // $this.$emit("search-fired", result.data.data);
            // $this.$store.commit("setHouseList", result.data.data);
          }
        },
        (error) => {
          throw new Error(error);
        }
      );
    } */
  }
};
</script>
