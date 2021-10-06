<template>
  <form class="filter-form">
    <b-row>
      <b-col cols="12" class="mt-47 m-mt-20 m-mb-10">
        <span class="theme-font-bold">Property type</span>
      </b-col>
      <b-col cols="12" lg="12" xl="12" class="mt-2">
        <b-row>
          <b-col
            sm="4"
            md="2"
            lg="2"
            class="property-types col-xs-4"
            :class="toggleActivate('RESI')"
          >
            <a
              ref="house"
              class=""
              href="#"
              @click.prevent="setPropertyType('RESI')"
            >
              <!-- <svg class="icon icon-home">
                <use
                  xlink:href="@/assets/scss/font/nexme-icons.svg#icon-home"
                ></use>
              </svg> -->
              <img
                src="@/assets/listing/house.svg"
                class="img-fluid mr-t"
                alt=""
              />
              <span>House</span>
            </a>
          </b-col>
          <b-col
            sm="4"
            md="2"
            lg="2"
            class="property-types col-xs-4"
            :class="toggleActivate('COND')"
          >
            <a
              ref="condominium"
              class=""
              href="#"
              @click.prevent="setPropertyType('COND')"
            >
              <svg class="icon icon-building">
                <use
                  xlink:href="@/assets/scss/font/nexme-icons.svg#icon-building"
                ></use>
              </svg>
              <span>Condominium</span>
            </a>
          </b-col>
          <b-col
            sm="4"
            md="2"
            lg="2"
            class="property-types col-xs-4"
            :class="toggleActivate('TSHR')"
          >
            <a
              ref="townhouse"
              class=""
              href="#"
              @click.prevent="setPropertyType('TSHR')"
            >
              <svg class="icon icon-town-house">
                <use
                  xlink:href="@/assets/icons/town-house.svg#icon-town-house"
                ></use>
              </svg>
              <span>Townhouse</span>
            </a>
          </b-col>
          <b-col
            sm="4"
            md="2"
            lg="2"
            class="property-types col-xs-4"
            :class="toggleActivate('MULT')"
          >
            <a
              ref="multi-family"
              class=""
              href="#"
              @click.prevent="setPropertyType('MULT')"
            >
              <svg class="icon icon-town-house">
                <use xlink:href="@/assets/icons/house2.svg#icon-house2"></use>
              </svg>
              <span>Multi-family</span>
            </a>
          </b-col>
          <b-col
            sm="4"
            md="2"
            lg="2"
            class="property-types col-xs-4"
            :class="toggleActivate('VACL')"
          >
            <a
              ref="land"
              class=""
              href="#"
              @click.prevent="setPropertyType('VACL')"
            >
              <svg class="icon icon-building">
                <use
                  xlink:href="@/assets/scss/font/nexme-icons.svg#icon-tree"
                ></use>
              </svg>
              <span>Land</span>
            </a>
          </b-col>
          <b-col
            sm="4"
            md="2"
            lg="2"
            class="property-types col-xs-4"
            :class="toggleActivate('MANU')"
          >
            <a
              ref="new-construction"
              class=""
              href="#"
              @click.prevent="setPropertyType('MANU')"
            >
              <svg class="icon icon-building">
                <use
                  xlink:href="@/assets/scss/font/nexme-icons.svg#icon-wrench"
                ></use>
              </svg>
              <span>New construction</span>
            </a>
          </b-col>
        </b-row>
      </b-col>
    </b-row>
    <div class="row mobile-view">
      <div class="col-12">
        <hr class="seperator m-mt-30 m-mb-30" />
      </div>
    </div>
    <b-row>
      <b-col class="mb-45 m-mt-10">
        <div class="bedsBathElements">
          <div class="theme-font-bold bed-bath d-flex">
            <span class="bed-bath-title">Bedrooms&thinsp;&ensp;</span>
            <div class="bathBedChage">
              <a class="inpMines" href="#" @click.prevent="decreaseBedrooms"
                >-</a
              >
              <span class="counter">
                {{ filterForm.criteria.beds.high }}+
              </span>
              <a href="#" class="inpPlus" @click.prevent="increaseBedrooms"
                >+</a
              >
            </div>
          </div>
          <div class="theme-font-bold bed-bath m-mt-25 d-flex">
            <span class="bed-bath-title">Bathrooms</span>
            <div class="bathBedChage">
              &shy;
              <a class="inpMines" href="#" @click.prevent="decreaseBathrooms"
                >-</a
              >
              <span class="counter">
                {{ filterForm.criteria.baths.high }}+
              </span>
              <a href="#" class="inpPlus" @click.prevent="increaseBathrooms"
                >+</a
              >
            </div>
          </div>
        </div>
      </b-col>
    </b-row>

    <b-row>
      <b-col cols="12" sm="6" md="6" lg="6" class="mb-45 m-mt-25">
        <label class="theme-font-bold">Square feet</label>
        <b-row class="form-select-container">
          <b-col cols="5" md="5" class="p-0">
            <b-dropdown class="dropDownItems">
              <template v-slot:button-content>
                {{
                  filterForm.criteria.square_feet.low !== 0
                    ? filterForm.criteria.square_feet.low
                    : "No min"
                }}
              </template>
              <b-dropdown-item href="#" @click.prevent="changeSqf(0, 'min')"
                >No min</b-dropdown-item
              >
              <b-dropdown-item
                v-for="Nm in minMaxSqf"
                :key="Nm.val"
                href="#"
                @click.prevent="changeSqf(Nm.val, 'min')"
                >{{ Nm.text }}</b-dropdown-item
              >
            </b-dropdown>
          </b-col>
          <span class="theme-font-light text-between-input">
            to
          </span>
          <b-col cols="5" md="5" class="p-0">
            <b-dropdown class="dropDownItems">
              <template v-slot:button-content>
                {{
                  filterForm.criteria.square_feet.high !== 0
                    ? filterForm.criteria.square_feet.high
                    : "No max"
                }}
              </template>
              <b-dropdown-item href="#" @click.prevent="changeSqf(0, 'high')"
                >No max</b-dropdown-item
              >
              <b-dropdown-item
                v-for="Nm in minMaxSqf"
                :key="Nm.val"
                href="#"
                @click.prevent="changeSqf(Nm.val, 'high')"
                >{{ Nm.text }}</b-dropdown-item
              >
            </b-dropdown>
          </b-col>
        </b-row>
      </b-col>
      <b-col cols="12" sm="6" md="6" lg="6" class="mb-45 m-mt-25">
        <label class="theme-font-bold">Year built</label>
        <b-row class="form-select-container">
          <b-col cols="5" md="5" class="p-0">
            <b-dropdown class="dropDownItems">
              <template v-slot:button-content>
                {{
                  filterForm.criteria.year_built.low !== 0
                    ? filterForm.criteria.year_built.low
                    : "No min"
                }}
              </template>
              <b-dropdown-item href="#" @click.prevent="changeYearB(0, 'min')"
                >No min</b-dropdown-item
              >
              <b-dropdown-item
                v-for="year in yearList"
                :key="year"
                href="#"
                @click.prevent="changeYearB(year, 'min')"
                >{{ year }}</b-dropdown-item
              >
            </b-dropdown>
          </b-col>
          <span class="theme-font-light text-between-input">
            to
          </span>
          <b-col cols="5" md="5" class="p-0">
            <b-dropdown class="dropDownItems">
              <template v-slot:button-content>
                {{
                  filterForm.criteria.year_built.high !== 0
                    ? filterForm.criteria.year_built.high
                    : "No max"
                }}
              </template>
              <b-dropdown-item href="#" @click.prevent="changeYearB(0, 'high')"
                >No max</b-dropdown-item
              >
              <b-dropdown-item
                v-for="year in yearList"
                :key="year"
                href="#"
                @click.prevent="changeYearB(year, 'high')"
                >{{ year }}</b-dropdown-item
              >
            </b-dropdown>
          </b-col>
        </b-row>
      </b-col>
    </b-row>
    <b-row>
      <b-col md="12" class="col-sm-6 col-md-6 col-lg-6 mb-40 m-mt-20">
        <label class="theme-font-bold">Max HOA fees</label>
        <b-row class="form-select-container">
          <b-col md="12" class="p-0">
            <b-dropdown class="dropDownItems">
              <template v-slot:button-content>
                {{ hoaTextVal }}
              </template>
              <b-dropdown-item
                v-for="Nm in maxHOAFeesMap()"
                :key="Nm.val"
                href="#"
                @click.prevent="changeHoa(Nm, 'min')"
                >{{ Nm.text }}</b-dropdown-item
              >
            </b-dropdown>
          </b-col>
        </b-row>
      </b-col>
      <b-col md="12" class="col-sm-6 col-md-6 col-lg-6 mb-30 m-mt-20">
        <label class="theme-font-bold">Status</label>
        <b-row class="form-select-container">
          <b-col md="12" class="p-0">
            <b-dropdown class="dropDownItems">
              <template v-slot:button-content>
                {{ statusText !== "" ? statusText : "Active listings" }}
              </template>
              <b-dropdown-item
                v-for="(Status, index) in newPropertyStatus"
                :key="index"
                href="#"
                @click.prevent="changeStatus(Status)"
                >{{ Status.val }}</b-dropdown-item
              >
            </b-dropdown>
          </b-col>
        </b-row>
      </b-col>
    </b-row>
    <b-row>
      <b-col cols="12" class="desktop-view">
        <hr class="hr-line" />
      </b-col>
    </b-row>
    <div class="row mobile-view">
      <div class="col-12">
        <hr class="seperator m-mt-30 m-mb-25" />
      </div>
    </div>
    <b-row>
      <b-col md="12" class="col-sm-6 col-md-6 col-lg-6 mb-40 m-mt-5">
        <label class="theme-font-bold">School rating</label>
        <b-row class="form-select-container">
          <b-col md="12" class="p-0">
            <b-dropdown class="dropDownItems">
              <template v-slot:button-content>
                {{
                  filterForm.criteria.minSchoolRating === 0
                    ? "No preference"
                    : filterForm.criteria.minSchoolRating + "+"
                }}
              </template>
              <b-dropdown-item @click.prevent="changeSchoolRate(0)">
                No preference
              </b-dropdown-item>
              <b-dropdown-item
                v-for="Status in 10"
                :key="Status"
                href="#"
                @click.prevent="changeSchoolRate(Status)"
                >{{ Status }}+</b-dropdown-item
              >
            </b-dropdown>
          </b-col>
        </b-row>
      </b-col>
      <b-col md="12" class="col-sm-6 col-md-6 col-lg-6 mb-40 m-mt-25">
        <label class="theme-font-bold">Time on Nexme</label>
        <b-row class="form-select-container">
          <b-col md="12" class="p-0">
            <b-dropdown class="dropDownItems">
              <template v-slot:button-content>
                {{ daysOnMarketText }}
              </template>
              <b-dropdown-item
                v-for="Status in daysOnMarketVal"
                :key="Status.val"
                href="#"
                @click.prevent="changeDaysOnMarket(Status)"
                >{{ Status.text }}</b-dropdown-item
              >
            </b-dropdown>
          </b-col>
        </b-row>
      </b-col>
    </b-row>
    <b-row>
      <b-col md="6" class="mb-50 m-mt-25 m-mb-46">
        <label class="theme-font-bold">Upcoming Open Houses</label>
        <b-row class="form-select-container">
          <b-col md="12" class="p-0">
            <b-dropdown class="dropDownItems">
              <template v-slot:button-content>
                {{ openHousetext }}
              </template>
              <b-dropdown-item
                v-for="(dayNm, index) in openHouseWeek()"
                :key="index"
                href="#"
                @click.prevent="changeOpenHouse(dayNm, index)"
                >{{ dayNm }}</b-dropdown-item
              >
            </b-dropdown>
          </b-col>
        </b-row>
      </b-col>
    </b-row>
    <b-row class="filter-buttons">
      <div v-if="filterError" class="col-md-12">
        <div class="filterError">
          {{ filterError }}
        </div>
      </div>
      <div class="col-md-6 col-sm-12">
        <button
          class="btn btn-primary btn-block theme-font-bold"
          @click.prevent="submitclicked()"
        >
          Apply and see {{ getListingCount ? getListingCount : 0 }} Homes
        </button>
      </div>
      <div class="col-md-6 col-sm-12 m-mt-10 fw-600">
        <a href="#" class="text-btn" @click.prevent="clearFilter()">
          Clear filters
        </a>
      </div>
    </b-row>
  </form>
</template>

<script>
import Axios from "axios";

export default {
  name: "ListingFilter",
  props: {
    listCount: {
      type: Number,
      default: 0
    },
    coordinates: {
      type: Object,
      default: () => {
        return {};
      }
    }
  },
  data() {
    return {
      yearList: [],
      listingCount: 0,
      minMaxSqf: [],
      formCriteria: false,
      filterOn: false,
      filterError: "",
      filterForm: {
        // latitude: this.coordinates.lat,
        // longitude: this.coordinates.lng,
        searchedText: "",
        searchRadius: 1200,
        criteria: {
          beds: {
            low: 0,
            high: 0
          },
          baths: {
            low: 0,
            high: 0
          },
          listingTypes: [],
          listingStatuses: [],
          price: {
            low: 0,
            high: 0
          },
          year_built: {
            low: 0,
            high: 0
          },
          square_feet: {
            low: 0,
            high: 0
          },
          homeowner_due: {
            low: null,
            high: null
          },
          minSchoolRating: 0,
          daysOnMarket: 0,
          openHouse_DaysFromNow: 0
        }
      },
      openHousetext: "Any",
      hoaTextVal: "No MAX",
      daysOnMarketText: "Any",
      daysOnMarketVal: [
        { val: 0, text: "Any" },
        { val: 1, text: "Newest listing" },
        { val: 2, text: "Less than 3 days" },
        { val: 3, text: "Less than 7 days" },
        { val: 4, text: "Less than 14 days" },
        { val: 5, text: "Less than 30 days" }
      ],
      newPropertyStatus: [
        { key: "A", val: "Active listings" },
        { key: "P", val: "Pending listings" },
        { key: "SoldLastWeek", val: "Sold in last 7 days" },
        { key: "SoldLastMonth", val: "Sold in last 30 days" },
        { key: "SoldLast60", val: "Sold in last 60 days" },
        { key: "SoldLast90", val: "Sold in last 90 days" },
        { key: "SoldLast180", val: "Sold in last 180 days" }
      ],
      propertyStatus: {},
      statusText: ""
    };
  },
  computed: {
    filterSubmitted() {
      return this.$store.getters.gethideFilter;
    },
    getListingCount() {
      return this.$store.getters.getMapMarkerData.length;
    }
  },
  watch: {
    filterSubmitted(d) {
      this.filterOn = d;
    },
    getListingCount(d) {
      this.listingCount = d.length;
    }
  },
  mounted() {
    this.yearList = this.getYearBuilt();
    this.minMaxSqf = this.sqFeet();
    const sText = localStorage.getItem("formattedAddress");
    this.searchedText = sText;
    this.propertyStatus = this.$store.getters.getFullPropertyStatus;
  },
  methods: {
    filterObject(form) {
      const bedsHigh = form.criteria.beds.high;
      const bathsHigh = form.criteria.baths.high;
      const yearBuiltLow = form.criteria.year_built.low;
      const yearBuiltHigh = form.criteria.year_built.high;
      const squareFeetLow = form.criteria.square_feet.low;
      const squareFeetHigh = form.criteria.square_feet.high;
      const openHouseFromNow = form.criteria.openHouse_DaysFromNow;
      const homeOwnerDueHigh = form.criteria.homeowner_due.high;
      const daysOnMarketVal = form.criteria.daysOnMarket;
      const listingTypesVal = this.filterForm.criteria.listingTypes;
      const listingStatusVal = this.filterForm.criteria.listingStatuses;
      const schoolRatingVal = form.criteria.minSchoolRating;
      const filterHistory = localStorage.getItem("filteredData");
      const searchedAddr = localStorage.getItem("formattedAddress");

      const formObj = {};

      formObj.searchText = searchedAddr;

      if (bedsHigh) {
        if (formObj.criteria === undefined) {
          formObj.criteria = {};
        }
        if (formObj.criteria.beds === undefined) {
          formObj.criteria.beds = {};
        }
        formObj.criteria.beds.low = bedsHigh;
      }

      if (bathsHigh) {
        if (formObj.criteria === undefined) {
          formObj.criteria = {};
        }
        if (formObj.criteria.baths === undefined) {
          formObj.criteria.baths = {};
        }
        formObj.criteria.baths.low = bathsHigh;
      }

      if (listingTypesVal.length > 0) {
        if (formObj.criteria === undefined) {
          formObj.criteria = {};
        }

        formObj.criteria.listingTypes = listingTypesVal;
      }

      if (filterHistory !== null) {
        const jsonPrice = JSON.parse(filterHistory);

        if (jsonPrice !== null && jsonPrice.criteria !== undefined) {
          if (jsonPrice.criteria.price !== undefined) {
            if (formObj.criteria === undefined) {
              formObj.criteria = {};
            }
            if (formObj.criteria.price === undefined) {
              formObj.criteria.price = {};
            }
            if (jsonPrice.criteria.price.low !== null) {
              formObj.criteria.price.low = jsonPrice.criteria.price.low;
            }

            if (jsonPrice.criteria.price.high !== null) {
              formObj.criteria.price.high = jsonPrice.criteria.price.high;
            }
          }
        }
      }

      if (yearBuiltLow) {
        if (formObj.criteria === undefined) {
          formObj.criteria = {};
        }
        if (formObj.criteria.year_built === undefined) {
          formObj.criteria.year_built = {};
        }
        formObj.criteria.year_built.low = yearBuiltLow;
      }

      if (yearBuiltHigh) {
        if (formObj.criteria === undefined) {
          formObj.criteria = {};
        }
        if (formObj.criteria.year_built === undefined) {
          formObj.criteria.year_built = {};
        }
        formObj.criteria.year_built.high = yearBuiltHigh;
      }

      if (squareFeetLow) {
        if (formObj.criteria === undefined) {
          formObj.criteria = {};
        }
        if (formObj.criteria.square_feet === undefined) {
          formObj.criteria.square_feet = {};
        }
        formObj.criteria.square_feet.low = squareFeetLow;
      }

      if (squareFeetHigh) {
        if (formObj.criteria === undefined) {
          formObj.criteria = {};
        }
        if (formObj.criteria.square_feet === undefined) {
          formObj.criteria.square_feet = {};
        }
        formObj.criteria.square_feet.high = squareFeetHigh;
      }

      if (homeOwnerDueHigh !== null) {
        if (formObj.criteria === undefined) {
          formObj.criteria = {};
        }
        if (formObj.criteria.homeowner_due === undefined) {
          formObj.criteria.homeowner_due = {};
        }
        if (homeOwnerDueHigh !== 0) {
          formObj.criteria.homeowner_due.high = homeOwnerDueHigh;
        } else {
          formObj.criteria.homeowner_due.min = 0;
          formObj.criteria.homeowner_due.high = 0;
        }
      }

      if (schoolRatingVal) {
        if (formObj.criteria === undefined) {
          formObj.criteria = {};
        }
        formObj.criteria.minSchoolRating = schoolRatingVal;
      }

      if (daysOnMarketVal) {
        if (formObj.criteria === undefined) {
          formObj.criteria = {};
        }
        formObj.criteria.daysOnMarket = daysOnMarketVal;
      }

      if (openHouseFromNow) {
        if (formObj.criteria === undefined) {
          formObj.criteria = {};
        }
        formObj.criteria.openHouse_DaysFromNow = openHouseFromNow;
      }

      if (listingStatusVal.length !== 0) {
        if (formObj.criteria === undefined) {
          formObj.criteria = {};
        }
        formObj.criteria.listingStatuses = listingStatusVal;
      }

      return formObj;
    },
    decreaseBedrooms() {
      this.formCriteria = true;
      if (this.filterForm.criteria.beds.high > 0) {
        this.filterForm.criteria.beds.high--;
      }
      this.submitclicked(false);
    },
    increaseBedrooms() {
      this.formCriteria = true;
      if (this.filterForm.criteria.beds.high < 10) {
        this.filterForm.criteria.beds.high++;
      }
      this.submitclicked(false);
    },
    decreaseBathrooms() {
      this.formCriteria = true;
      if (this.filterForm.criteria.baths.high > 0) {
        this.filterForm.criteria.baths.high -= 0.5;
      }
      this.submitclicked(false);
    },
    increaseBathrooms() {
      this.formCriteria = true;
      if (this.filterForm.criteria.baths.high < 10) {
        this.filterForm.criteria.baths.high =
          this.filterForm.criteria.baths.high + 0.5;
      }
      this.submitclicked(false);
    },
    setPropertyType(type) {
      this.formCriteria = true;
      const arr = this.filterForm.criteria.listingTypes;

      if (arr.includes(type)) {
        arr.forEach(function(element, index) {
          if (element === type) arr.splice(index, 1);
        });
      } else {
        arr.push(type);
      }
      this.filterForm.criteria.listingTypes = arr;
      this.submitclicked(false);
    },
    submitclicked(filters = true, clear = false) {
      this.$store.commit("setSearchFierd", false);
      const mapInfo = this.$store.getters.getMapInfo;
      this.$store.commit("setApiLoadingStatus", true);
      if (filters) {
        this.$store.commit("sethideFilter", false);
      }
      this.$store.commit("setSearchStarted", true);
      const This = this;
      let filtObj = {};
      if (clear) {
        filtObj = {};
      } else {
        filtObj = this.filterObject(This.filterForm);
      }
      filtObj.latitude = mapInfo.location.lat;
      filtObj.longitude = mapInfo.location.lng;
      filtObj.searchRadius = mapInfo.zoom;

      const searchedAddress = localStorage.getItem("formattedAddress");
      if (searchedAddress) {
        const Url = this.$apiPath("main", "Listings/Filter", "");
        Axios({
          method: "POST",
          url: Url,
          data: JSON.stringify(filtObj),
          headers: {
            "Content-Type": "application/json",
            "X-Client-Token": This.$store.state.X_CLIENT_TOKEN
          }
        }).then(
          (result) => {
            localStorage.setItem("filteredData", JSON.stringify(filtObj));
            if (result.data.statusCode === "Success") {
              This.$store.commit("setHouseList", result.data.data);
              This.$emit("search-fired", result.data.data);
              This.finalSearchedData(result.data.data, false);
              This.$store.commit("setApiLoadingStatus", false);
              // This.$emit('updateList',null);
            }
          },
          (error) => {
            throw new Error(error);
          }
        );
      } else {
        this.filterError = "You need to search an area first to apply filters.";
      }
    },
    getYearBuilt() {
      this.formCriteria = true;
      const date = new Date();
      const currentYear = date.getFullYear();
      const listYears = [];
      let startYear = 1900;
      listYears.push(startYear);
      while (startYear < currentYear) {
        if (this.dateCheck(1900, 1989, startYear)) {
          startYear += 10;
        } else if (this.dateCheck(1990, 1994, startYear)) {
          startYear += 1;
        } else {
          startYear += 1;
        }
        listYears.push(startYear);
      }
      listYears.reverse();
      const yearsMap = {};
      listYears.forEach((it) => {
        yearsMap[it.toString()] = it;
      });
      const finalLst = listYears.sort(function(a, b) {
        return b - a;
      });
      return finalLst;
    },
    dateCheck(from, to, check) {
      const fDate = Date.parse(from);
      const lDate = Date.parse(to);
      const cDate = Date.parse(check);

      if (cDate <= lDate && cDate >= fDate) {
        return true;
      }
      return false;
    },
    clearFilter() {
      this.filterForm.criteria = {
        beds: {
          low: 0,
          high: 0
        },
        baths: {
          low: 0,
          high: 0
        },
        listingTypes: [],
        listingStatuses: ["A"],
        price: {
          low: 0,
          high: 0
        },
        year_built: {
          low: 0,
          high: 0
        },
        square_feet: {
          low: 0,
          high: 0
        },
        homeowner_due: {
          low: 0,
          high: 0
        },
        minSchoolRating: 0,
        daysOnMarket: null,
        openHouse_DaysFromNow: null
      };
      this.filterForm.criteria.beds.low = 0;
      this.filterForm.criteria.baths.low = 0;
      this.filterForm.criteria.listingTypes = [];
      this.filterForm.criteria.listingStatuses = [];
      this.filterForm.criteria.price = {};
      this.filterForm.criteria.year_built.low = 0;
      this.filterForm.criteria.year_built.high = 0;
      this.filterForm.criteria.square_feet.low = 0;
      this.filterForm.criteria.square_feet.high = 0;
      this.filterForm.criteria.homeowner_due.high = null;
      this.hoaTextVal = "No Max";
      this.filterForm.minSchoolRating = 0;
      this.filterForm.daysOnMarket = null;
      this.filterForm.openHouse_DaysFromNow = null;

      this.daysOnMarketText = "Any";
      this.openHousetext = "Any";
      this.statusText = "Active";
      localStorage.removeItem("filteredData");
      const $this = this;
      this.$store.commit("setPriceFiltered", true);
      setTimeout(() => {
        $this.submitclicked(false, true);
      }, 500);
    },
    toggleActivate(type) {
      if (this.filterForm.criteria.listingTypes.includes(type)) {
        return "active";
      } else {
        return "";
      }
    },
    addComma(value) {
      const number = value.toString();
      if (number.includes(",")) {
        const allNum = number.replace(/,/g, "");
        return allNum.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
      } else {
        return number.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
      }
    },
    sqFeet() {
      const sqf = [];
      for (let i = 1; i < 41; i++) {
        if (i === 1) {
          // sqf.push(i * 500);
        } else {
          const lst = {
            text: this.addComma(i * 250),
            val: i * 250
          };
          sqf.push(lst);
        }
      }

      return sqf;
    },
    changeSqf(val, type) {
      this.formCriteria = true;
      if (type === "min") {
        this.filterForm.criteria.square_feet.low = val;
      } else {
        this.filterForm.criteria.square_feet.high = val;
      }
      this.submitclicked(false);
    },
    changeYearB(val, type) {
      this.formCriteria = true;
      if (type === "min") {
        this.filterForm.criteria.year_built.low = val;
      } else {
        this.filterForm.criteria.year_built.high = val;
      }
      this.submitclicked(false);
    },
    changeHoa(val) {
      this.formCriteria = true;
      this.hoaTextVal = val.text;
      this.filterForm.criteria.homeowner_due.high = val.val;
      this.submitclicked(false);
    },
    changeStatus(Status) {
      this.formCriteria = true;
      this.statusText = Status.val;
      this.filterForm.criteria.listingStatuses = [Status.key];
      this.submitclicked(false);
    },
    changeSchoolRate(Status) {
      this.formCriteria = true;
      this.filterForm.criteria.minSchoolRating = Status;
      this.submitclicked(false);
    },
    changeDaysOnMarket(Status) {
      this.formCriteria = true;
      this.daysOnMarketText = Status.text;
      this.filterForm.criteria.daysOnMarket = Status.val;
      this.submitclicked(false);
    },
    changeOpenHouse(text, index) {
      this.openHousetext = text;
      this.filterForm.criteria.openHouse_DaysFromNow = index;
      this.submitclicked(false);
    },
    ordinal_suffix_of(i) {
      const j = i % 10;
      const k = i % 100;
      if (j === 1 && k !== 11) {
        return i + "st";
      }
      if (j === 2 && k !== 12) {
        return i + "nd";
      }
      if (j === 3 && k !== 13) {
        return i + "rd";
      }
      return i + "th";
    },
    openHouseWeek() {
      const myDays = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
        "Sunday"
      ];
      const monthNames = [
        "Jan",
        "Feb",
        "Mar",
        "Apr",
        "May",
        "Jun",
        "Jul",
        "Aug",
        "Sep",
        "Oct",
        "Nov",
        "Dec"
      ];

      const dayNames = [];
      for (let i = 0; i <= 6; i++) {
        const dayNumber = new Date(
          new Date().getTime() + i * 24 * 60 * 60 * 1000
        );
        const monShort = monthNames[dayNumber.getMonth()];
        if (i === 0) {
          dayNames.push(
            `Today, ${monShort} ${this.ordinal_suffix_of(dayNumber.getDate())}`
          );
        } else if (i === 1) {
          dayNames.push(
            `Tomorrow, ${monShort} ${this.ordinal_suffix_of(
              dayNumber.getDate()
            )}`
          );
        } else {
          dayNames.push(
            `${
              myDays[dayNumber.getDay()]
            }, ${monShort} ${this.ordinal_suffix_of(dayNumber.getDate())}`
          );
        }
      }

      return dayNames;
    },
    maxHOAFeesMap() {
      const listHOAFees = [
        25,
        50,
        75,
        100,
        200,
        300,
        400,
        500,
        600,
        700,
        800,
        900,
        1000,
        1500,
        2000,
        2500,
        3000,
        3500,
        4000,
        5000,
        6000,
        7000,
        8000,
        9000,
        10000
      ];

      const hoaFeesMap = [];
      hoaFeesMap.push({ text: "No HOA", val: 0 });
      hoaFeesMap.push({ text: "No max", val: 200000 });
      listHOAFees.forEach((nm) => {
        hoaFeesMap.push({ text: `$${nm}/month`, val: nm });
      });

      return hoaFeesMap;
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
    }
  }
};
</script>
