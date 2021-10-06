<template>
  <div class="customTextinput-wrapper">
    <form
      class="getStartedPhoneCheck"
      @submit.prevent="$emit('on-submit', phone)"
    >
      <b-input
        v-if="inputTypeCheck('search')"
        v-model="text"
        class="searchInput inpA"
        type="text"
        :placeholder="placeholderText"
        maxlength="10"
      />
      <gmap-autocomplete
        v-if="inputTypeCheck('gmapAuto')"
        class="form-control searchInput inpB"
        :placeholder="placeholderText"
        @input="storeText"
        @listeners="checkGinputVal($event)"
        @keyup="checkInputValue($event)"
        @place_changed="getAddress"
      >
      </gmap-autocomplete>

      <div
        v-if="inputTypeCheck('phone')"
        class="phoneNumberCheck inpC vue-tel-input"
      >
        <div tabindex="0" class="vti__dropdown">
          <b-dropdown
            id="flagDropDown"
            size="lg"
            variant="link"
            toggle-class="text-decoration-none"
            no-caret
          >
            <template v-slot:button-content>
              <div class="selectedFlag" :class="flag"></div>
              <span class="vti__selection">
                <span class="vti__dropdown-arrow">▼</span>
              </span>
            </template>
            <b-dropdown-item @click="changeCountry('us', 1)">
              <div class="selectedFlag us"></div>
              <div class="countryName">USA</div>
            </b-dropdown-item>
            <b-dropdown-item @click="changeCountry('au', 61)">
              <div class="selectedFlag au"></div>
              <div class="countryName">Australia</div>
            </b-dropdown-item>
            <b-dropdown-item @click="changeCountry('ca', 1)">
              <div class="selectedFlag ca"></div>
              <div class="countryName">Canada</div>
            </b-dropdown-item>
            <b-dropdown-item @click="changeCountry('hk', 852)">
              <div class="selectedFlag hk"></div>
              <div class="countryName">Hong Kong</div>
            </b-dropdown-item>
            <b-dropdown-item @click="changeCountry('mx', 52)">
              <div class="selectedFlag mx"></div>
              <div class="countryName">Mexico</div>
            </b-dropdown-item>
            <b-dropdown-item @click="changeCountry('jp', 81)">
              <div class="selectedFlag jp"></div>
              <div class="countryName">Japan</div>
            </b-dropdown-item>
            <b-dropdown-item @click="changeCountry('uk', 44)">
              <div class="selectedFlag uk"></div>
              <div class="countryName">United Kingdom</div>
            </b-dropdown-item>
            <b-dropdown-item @click="changeCountry('ch', 86)">
              <div class="selectedFlag ch"></div>
              <div class="countryName">China</div>
            </b-dropdown-item>
          </b-dropdown>
        </div>
        <input
          id=""
          v-model="phone"
          v-format-phone
          type="tel"
          :placeholder="placeholderText"
          autocomplete="off"
          name="phone"
          maxlength="14"
          tabindex="0"
          class="vti__input phoneNumberLg"
          @keyup="addBracket($event)"
        />
        <!-- @keypress="formatNumber($event)" -->
      </div>

      <div
        class="submit"
        :style="showSearch ? 'max-width:105px;' : 'max-width:150px;'"
        align-v="center"
      >
        <button
          v-if="submitText"
          type="submit"
          class="searchButton d-flex align-items-center justify-content-center"
          :disabled="searchButton"
          @click="
            $emit('on-submit', phone);
            showPhoneModal();
            setCountryCode();
          "
        >
          <!-- <button type="submit" @click="submitPhoneNumber">{{submitText}}</button> -->
          <span
            class="theme-font-semi-bold sImg justify-content-center desktop-view"
          >
            {{ submitText }}
          </span>
          <span
            class="theme-font-semi-bold sImg justify-content-center mobile-view"
          >
            <img src="@/assets/icons/right-arrow.svg" alt="" />
          </span>
        </button>
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
        id="getPhoneNumber"
        centered
        hide-footer
        title="Sell a home is not available on web yet."
      >
        <p class="my-1 pt-0 mb-4 mt-0 font-17 fw-400">
          Sell a home is coming soon for web! Feel free to Sell a home through
          our mobile app and get started right away.
        </p>
        <div class="d-flex justify-content-around">
          <a href="#" class="col-sm-6" target="_blank">
            <img
              src="@/assets/icons/apple_logo.png"
              class="img-fluid"
              alt="App Store"
            />
          </a>
          <a
            href="https://play.google.com/store/apps/details?id=com.nexme"
            target="_blank"
            class="col-sm-6"
          >
            <img
              src="@/assets/icons/google_logo.png"
              class="img-fluid"
              alt="Play Store"
            />
          </a>
        </div>
        <h6 v-if="false" class="pt-3">
          {{
            messageText == ""
              ? "We’ll text you an app download link!"
              : messageText
          }}
        </h6>
        <div v-if="false" class="input-group col-md-8 col-sm-12 col-sx-12 pl-0">
          <input
            v-model="appPhone"
            v-format-phone
            type="text"
            class="form-control formAttachedInput"
            placeholder="(123) 456-7890"
            maxlength="14"
          />
          <button
            class="input-group-append formAttachedBtn"
            :disabled="!appPhone"
            @click="submitAppRequest()"
          >
            Send
          </button>
        </div>
        <p v-if="false">
          Standard text messaging rates apply
        </p>
      </b-modal>
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
    <!-- <div v-if="searchButton" class="searchCover"></div> -->
  </div>
</template>

<script>
import Axios from "axios";
import { Helper } from "@/plugins/mixins/helper";
// import "vue-phone-number-input/dist/vue-phone-number-input.css";
export default {
  name: "CustomTextInput",
  mixins: [Helper],
  props: {
    placeholderText: {
      type: String,
      default: ""
    },
    showLocationIcon: Boolean,
    showDollar: Boolean,
    showCountry: Boolean,
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
      phone: "",
      appPhone: "",
      inputElement: "",
      modalTitle: "",
      letterCount: 0,
      countryCode: 1,
      countryCodeStr: "1",
      searchButton: false,
      apiLoading: false,
      showEnterNumber: false,
      mapDetails: {},
      flag: "us",
      searchInput: "",
      messageText: "We’ll text you an app download link!",
      bindProps: {
        mode: "international",
        defaultCountry: "US",
        disabledFetchingCountry: false,
        disabled: false,
        disabledFormatting: false,
        placeholder: "Enter your mobile number",
        required: false,
        enabledCountryCode: false,
        enabledFlags: true,
        autocomplete: "off",
        name: "phone",
        maxLen: 11,
        inputClasses: "phoneNumberLg",
        onlyCountries: ["US", "CA", "MX"]
      },
      cityGeometry: {},
      searchedVal: ""
    };
  },
  computed: {
    inputstyle() {
      if (this.showLocationIcon || this.showCountry) {
        if (this.showLocationIcon) {
          return "padding-left: 50px;";
        }

        return "padding-left: 70px;";
      }
      return "";
    },
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
    this.$store.commit("setLoadingBcomeAgentBtn", false);
  },
  methods: {
    changeCountry(flag, code) {
      this.countryCode = code;
      this.flag = flag;
      this.$store.commit("setCountryCode", code);
    },
    setCountryCode() {
      this.$store.commit("setCountryCode", this.countryCode);
    },
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
      const lc = JSON.stringify(place.geometry.location);
      const mapDetails = {
        location: JSON.parse(lc)
      };
      this.mapDetails = mapDetails;

      this.$store.commit("setGeometry", mapDetails);
      localStorage.setItem("geometry", JSON.stringify(mapDetails));
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
          this.searchByText();
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

      const searchData = {
        searchText: selected
      };

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
          //   This.search = null;
          if (result.data.statusCode === "Success") {
            if (result.data.data.length === 0) {
              $this.searchFromFilterApi();
            } else {
              if (result.data.data.length === 1) {
                const state = result.data.data[0].state;
                const mls = result.data.data[0].mls;
                $this.$router.push({
                  name: "home-state-mls",
                  params: { state, mls }
                });
              } else if (result.data.data.length > 1) {
                // this.$emit("on-submit", this.text);
                const mapGeo = {
                  location: {
                    lat: $this.mapDetails.location.lat,
                    lng: $this.mapDetails.location.lng
                  }
                };
                localStorage.setItem("geometry", JSON.stringify(mapGeo));
                // $this.$store.commit("setHouseList", result.data.data);
                $this.$store.commit("setCityGeometry", $this.cityGeometry);
                $this.manageSearchedData(result.data.data);
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
      this.apiLoading = true;
      this.$store.commit("setSearchFierd", true);
      const input = this.inputElement;
      const $this = this;
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

                $this.manageSearchedData(result.data.data);
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
                    location: {
                      lat: $this.mapDetails.location.lat,
                      lng: $this.mapDetails.location.lng
                    }
                  };
                  // $this.$store.commit("setHouseList", result.data.data);
                  $this.manageSearchedData(result.data.data);
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
          console.log(result);
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
          console.log(results);
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
    storeResultData(res) {
      // const data = res;
      // console.log(this.soldDate("11/22/2016"));
      // const mlsList = [];
      // const mapMarkerData = [];
      // this.$store.commit("setTotalDataCount", data.length);
      // for (let d = 0; d <= data.length; d++) {
      //   if (data[d] !== undefined) {
      //     const marker = {
      //       position: {
      //         lat: data[d].latitude,
      //         lng: data[d].longitude
      //       },
      //       info: {
      //         mls: data[d].mls,
      //         listingPrice: data[d].listingPrice,
      //         hasOpenHouse: data[d].hasOpenHouse,
      //         state: data[d].state
      //       }
      //     };
      //
      //     mapMarkerData.push(marker);
      //     mlsList.push(data[d].mls);
      //   }
      // }
      //
      // this.$store.commit("setMapMarketData", mapMarkerData);
      //
      // let i;
      // let j;
      // let pageData;
      // let rightData;
      // const tempList = [];
      // const rightListingData = [];
      // const chunk = 20;
      // for (i = 0, j = mlsList.length; i < j; i += chunk) {
      //   pageData = mlsList.slice(i, i + chunk);
      //   rightData = data.slice(i, i + chunk);
      //   tempList.push(pageData);
      //   rightListingData.push(rightData);
      // }
      //
      // this.$store.commit("setListingsMls", tempList);
      // this.$store.commit("setListingsChunk", rightListingData);
      // ------------------------
      // this.mlsPerPage = tempList;
      //
      // const mlsListData = tempList;
      // const selectedMls = this.selectedMarker || 1;
      // for (let t = 0; t <= mlsListData.length; t++) {
      //   if (mlsListData[t] !== undefined) {
      //     if (mlsListData[t].includes(selectedMls)) {
      //       this.$store.commit("setSelectedPage", t);
      //     }
      //   }
      // }
      //
      // this.$router.push("listings");
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
        "You will receive a message shortly with link to download the Nexme app.";
      setTimeout(function() {
        This.$bvModal.hide("getPhoneNumber");
      }, 2000);
    },
    changeContry() {
      console.log(this.countryCodeStr);
    },
    changeContryB($event) {
      console.log($event);
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
