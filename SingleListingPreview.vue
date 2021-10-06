<template>
  <b-row class="homeListCards">
    <div class="favoriteIcon desktop-view">
      <img
        v-if="!propertyInFavList"
        :ref="`like-${listing.mls}`"
        src="@/assets/icons/favorite_clear_new.svg"
        width="40"
        height="36"
        @click="like(listing.mls)"
      />
      <img
        v-else
        :ref="`dislike-${listing.mls}`"
        src="@/assets/icons/favorite_red.svg"
        width="40"
        height="36"
        @click="disLike(listing.mls)"
      />
    </div>
    <div class="favoriteIcon mobile-view">
      <img
        v-if="!propertyInFavList"
        :ref="`like-${listing.mls}`"
        src="@/assets/icons/favorite_clear.svg"
        width="40"
        height="36"
        @click="like(listing.mls)"
      />
      <img
        v-else
        :ref="`dislike-${listing.mls}`"
        src="@/assets/icons/favorite_red.svg"
        width="40"
        height="36"
        @click="disLike(listing.mls)"
      />
    </div>
    <b-col
      cols="12"
      class="bgcard d-sm-none"
      :style="
        'background: linear-gradient(0deg, rgba(72, 70, 70, 0.9) 12%, rgba(22, 22, 22, 0) 60.61%), url(' +
          listing.thumbnailURL +
          ') no-repeat;'
      "
      @click="showDetails"
    >
      <div class="status-wrapper">
        <div
          v-if="listing.hasOpenHouse"
          class="openHouseIcon mobile-listPr"
          :class="hasLiveStream() ? 'liveStreamPr' : ''"
        >
          <img v-if="!hasLiveStream()" src="@/assets/icons/open_sign.svg" />
          <div class="time-view">
            <div v-if="hasLiveStream()" class="liveText">
              LIVE STREAM
            </div>
            {{ openHouseTime(listing.openHouses[0]) }}
          </div>
        </div>
        <div
          v-if="listing.status && listing.status !== 'A'"
          class="property-status"
          :class="crStatus.class"
        >
          {{ crStatus.status }}
          {{ crStatus.status === "Sold" ? soldDate(listing.sellingDate) : "" }}
        </div>
      </div>

      <div class="mobile-listing">
        <b-row>
          <b-col cols="6" class="pl-2 mt-2">
            <span class="theme-font-semi-bold top-text-a"
              >${{ numberWithCommas(listing.listingPrice) }}</span
            >
          </b-col>
          <b-col cols="6" class="pl-2 pr-0 mt-2">
            <b-row>
              <b-col cols="4" class="property-features pr-0 pl-0">
                <b-row
                  class="theme-font-semi-bold justify-content-center top-text-a"
                >
                  {{ listing.bedrooms }}
                </b-row>
                <b-row class="theme-font-light justify-content-center c">
                  Beds
                </b-row>
              </b-col>
              <b-col cols="4" class="property-features pr-0 pl-0">
                <b-row
                  class="theme-font-semi-bold justify-content-center top-text-a"
                >
                  {{ listing.baths }}
                </b-row>
                <b-row class="theme-font-light justify-content-center c">
                  Baths
                </b-row>
              </b-col>
              <b-col cols="4" class="property-features pr-0 pl-0">
                <b-row
                  class="theme-font-semi-bold justify-content-center top-text-a"
                >
                  {{ numberWithCommas(listing.totalSquareFoot) }}
                </b-row>
                <b-row class="theme-font-light justify-content-center c">
                  Sq. ft.
                </b-row>
              </b-col>
            </b-row>
          </b-col>
        </b-row>
        <b-row>
          <b-col cols="8" class="address-text pl-2">
            <b-row>
              <span class="theme-font-regular c"
                >{{ listing.house }}{{ listing.directionalPrefix
                }}{{ listing.street }} {{ listing.streetSuffix }}
                {{ listing.directionalSuffix }} {{ listing.unit }}</span
              >
            </b-row>
            <b-row>
              <span class="theme-font-regular c"
                >{{ listing.city }}&nbsp;{{ listing.state }}&nbsp;{{
                  listing.zip
                }}</span
              >
            </b-row>
          </b-col>
        </b-row>
        <div class="mls-num-mobile">
          <span class="theme-font-light">MLS&nbsp;{{ listing.mls }}</span>
        </div>
      </div>
    </b-col>
    <b-row class="bglong d-none d-sm-block overflow-hidden">
      <b-row no-gutters>
        <b-row
          class="img"
          :style="'background: url(' + listing.thumbnailURL + ') no-repeat;'"
          @click="showDetails"
        >
          <b-row class="status-wrapper">
            <div v-if="hasOpenHouseToday()" class="openHouseIcon">
              <!-- listing.hasOpenHouse -->
              <img src="@/assets/icons/open_sign.svg" />
              <div class="time-view">
                {{ openHouseTime(listing.openHouses[0]) }}
              </div>
            </div>
            <div
              v-if="listing.status && listing.status !== 'A'"
              class="property-status"
              :class="crStatus.class"
            >
              {{ crStatus.status }}
              {{
                crStatus.status === "Sold" ? soldDate(listing.sellingDate) : ""
              }}
            </div>
          </b-row>
        </b-row>
        <b-col class="ml-3">
          <b-row>
            <b-col cols="11" @click="showDetails">
              <p class="theme-font-semi-bold addr">
                {{ listing.houseNumber }} {{ listing.street }}
                {{ listing.directionalPrefix }} {{ listing.streetSuffix }}
                {{ listing.directionalSuffix }} #{{ listing.unit }}
                {{ listing.city }} {{ listing.state }} {{ listing.zip }}
              </p>
            </b-col>
          </b-row>
          <b-row @click="showDetails">
            <b-col cols="12">
              <p class="font-14 mb-1">
                <span class="theme-font-bold price">
                  ${{ numberWithCommas(listing.listingPrice) }}
                </span>
                {{ listing.bedrooms }} bd | {{ listing.baths }} ba |
                {{ numberWithCommas(listing.totalSquareFoot) }} sq. ft.
              </p>
            </b-col>
          </b-row>
          <b-row class="cashback theme-font-regular">
            <b-col class="mt-2">
              <img src="@/assets/icons/cashback.svg" class="mb-1" />
              <span @click="showDetails">&nbsp;Cash Back</span>
              <span class="secondarycolor" @click="showDetails"
                >&nbsp;${{
                  numberWithCommas((listing.listingPrice * 0.015).toFixed(2))
                }}&nbsp;</span
              >
              <img
                v-b-popover.hover.top="cashBackInfo"
                src="@/assets/icons/info.svg"
                class="mb-1 ml-1"
                title="What is cash back?"
                variant="primary"
              />
            </b-col>
          </b-row>
          <div class="dateText" @click="showDetails">
            <b-col class="end-note">
              <span class="theme-font-regular days float-left">
                {{ listing.daysOnMarket }} days on the market</span
              >
              <span class="theme-font-regular days float-right"
                >MLS {{ listing.mls }}</span
              >
            </b-col>
          </div>
        </b-col>
      </b-row>
    </b-row>
    <startLogin
      v-if="launchLogin"
      :start-login-box="launchLogin"
      @showLogin="unmountComp"
    ></startLogin>
  </b-row>
</template>
<script>
import axios from "axios";
import startLogin from "@/components/layout/login/loginModals";
import { Helper } from "@/plugins/mixins/helper";

export default {
  name: "SingleListingPreview",
  components: {
    startLogin
  },
  mixins: [Helper],
  props: {
    listing: {
      type: Object,
      default: () => {
        return {};
      }
    },
    id: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      launchLogin: false,
      modalTitle: "",
      contentVisible: false,
      className: "",
      propertyInFavList: false,
      propertyStatus: {},
      favoritedList: [],
      crStatus: {},
      cashBackInfo:
        "Traditionally, real estate agents charge up to a 3% buyer’s agent commission. This is the amount the seller is offering to a buyer’s agent. Our local Nexme agent partners commit to a 50% reduction in commission and we pass on the remaining 50% back to you. Nexme does not take a commission fee."
    };
  },
  computed: {
    checkMls() {
      return this.listing.mls;
    }
  },
  watch: {
    checkMls(d) {
      this.isFavorited();
    }
  },
  mounted() {
    if (this.listing.status) {
      this.propertyStatus = this.$store.getters.getPropertyStatus;
      this.className = this.propStatus(
        this.$store.getters.getPropertyStatus,
        this.listing.status,
        "cl"
      );
    }

    if (this.listing.status) {
      this.crStatus = this.statusMaker(
        this.$store.getters.getFullPropertyStatus,
        this.listing.status
      );
    }

    const userAuth = localStorage.getItem("userToken");
    if (userAuth !== null) {
      if (localStorage.getItem("favoriteList") !== null) {
        const favList = JSON.parse(localStorage.getItem("favoriteList"));
        this.favoritedList = favList;
        if (this.favoritedList.includes(this.listing.mls)) {
          this.propertyInFavList = true;
        }
      }
    }
  },
  methods: {
    hasLiveStream() {
      return (
        this.listing.openHouses[0] && this.listing.openHouses[0].type === "VIR"
      );
    },
    isFavorited() {
      const userAuth = localStorage.getItem("userToken");
      if (userAuth !== null) {
        if (localStorage.getItem("favoriteList") !== null) {
          const favList = JSON.parse(localStorage.getItem("favoriteList"));
          this.favoritedList = favList;
          if (this.favoritedList.includes(this.listing.mls)) {
            this.propertyInFavList = true;
          } else {
            this.propertyInFavList = false;
          }
        }
      }
    },
    numberWithCommas(x) {
      return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },
    hasOpenHouseToday() {
      if (this.listing.hasOpenHouse) {
        const tm = this.listing.openHouses[0];
        const op = new Date(tm.open);
        const open = op.getUTCDate();
        const td = new Date();
        const today = td.getUTCDate();
        return open === today;
      }
      return false;
    },
    openHouseTime(time) {
      const open = new Date(time.open);
      const close = new Date(time.close);
      const finalTime =
        this.formatAMPM(open, true) + " - " + this.formatAMPM(close, true);
      return finalTime;
    },
    showDetails() {
      // this.$store.dispatch("showDetails", this.listing.mls);
      // this.$router.push("/homes/" + this.listing.mls);
      const ds = this.listing.directionalSuffix
        ? `-${this.listing.directionalSuffix}`
        : "";
      const unit = this.listing.unit ? `-${this.listing.unit}` : "";
      const dp = this.listing.directionalPrefix
        ? `-${this.listing.directionalPrefix}`
        : "";
      const ss = this.listing.streetSuffix
        ? `-${this.listing.streetSuffix}`
        : "";
      const Address = `${this.listing.houseNumber}-${this.listing.street}${ss}${dp}${ds}${unit}`;
      console.log(Address);
      const routeData = this.$router.resolve({
        name: "home-state-mls",
        params: {
          state: this.listing.state,
          mls: this.listing.mls
        }
      });
      window.open(routeData.href, "_blank");
      // window.open("#/homes/" + this.listing.mls, "_blank");
    },
    like(mls) {
      const This = this;
      // user validation for logged in user place here.
      const Url = this.$apiPath("main", "Favorites/Add/", mls);
      if (this.$store.state.X_USER_TOKEN.length > 0) {
        axios({
          method: "POST",
          url: Url,
          headers: {
            "Content-Type": "application/json",
            "X-Client-Token": This.$store.state.X_CLIENT_TOKEN,
            "X-User-Token": This.$store.state.X_USER_TOKEN
          },
          data: {
            listing_mls: mls
          }
        }).then(
          (result) => {
            if (result.data.statusCode === "Success") {
              // This.favoritedList.push(mls);
              if (localStorage.getItem("favoriteList") !== null) {
                const favState = This.$store.getters.getfavoriteList;
                const favList = favState; // This.favoritedList;

                This.favoritedList = favList;
                This.propertyInFavList = true;
                This.$store.commit("setAddfavoriteList", `${mls}`);
                localStorage.setItem("favoriteList", JSON.stringify(favList));
              } else {
                localStorage.setItem(
                  "favoriteList",
                  JSON.stringify(`[${mls}]`)
                );
              }
            }
          },
          (error) => {
            throw new Error(error);
          }
        );
      } else {
        this.launchLogin = true;
      }
    },
    disLike(mls) {
      const This = this;
      const Url = this.$apiPath("main", "Favorites/Remove/", mls);
      if (this.$store.state.X_USER_TOKEN.length > 0) {
        // user logged in
        axios({
          method: "POST",
          url: Url,
          headers: {
            "Content-Type": "application/json",
            "X-Client-Token": This.$store.state.X_CLIENT_TOKEN,
            "X-User-Token": This.$store.state.X_USER_TOKEN
          },
          data: {
            listing_mls: mls
          }
        }).then(
          (result) => {
            if (result.data.statusCode === "Success") {
              // This.$refs[`dislike-${mls}`].style.display = "none";
              // This.$refs[`like-${mls}`].style.display = "inline";
              if (localStorage.getItem("favoriteList") !== null) {
                const favs = JSON.parse(localStorage.getItem("favoriteList"));
                const removeMls = favs.filter((e) => e !== mls);
                This.favoritedList = removeMls;
                localStorage.setItem("favoriteList", JSON.stringify(removeMls));
                This.$store.commit("setfavoriteList", removeMls);
                This.propertyInFavList = false;
              }
            }
          },
          (error) => {
            throw new Error(error);
          }
        );
      }
    },
    unmountComp() {
      this.launchLogin = false;
    }
  }
};
</script>
