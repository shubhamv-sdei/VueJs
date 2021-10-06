<template>
  <b-row class="schedule-tour-widget">
    <b-row class="first">
      <b-col cols="4">
        <img
          :src="
            listing.imageList.length > 0
              ? listing.imageBaseURL + listing.imageList[0]
              : ''
          "
          class="a"
          width="106"
          height="106"
        />
      </b-col>
      <b-col cols="8" class="addr">
        <b-row>
          <span class="address theme-font-regular"
            >{{ listing.house }} {{ listing.directionalPrefix }}
            {{ listing.street }} {{ listing.streetSuffix }}
            {{ listing.directionalSuffix }}</span
          >
        </b-row>
        <b-row>
          <span class="address theme-font-regular"
            >{{ listing.unit ? "#" + listing.unit : "" }} {{ listing.city }}
            {{ listing.state }} {{ listing.zip }}</span
          >
        </b-row>
      </b-col>
    </b-row>
    <b-row>
      <b-row
        v-show="step === 0"
        class="confirm theme-font-semi-bold justify-content-center"
      >
        <b-col cols="1"></b-col>
        <b-col cols="10">
          <b-row class="lbl">
            <b-col>
              <span>When are you able to tour?</span>
            </b-col>
          </b-row>
          <b-row>
            <b-col cols="2"></b-col>
            <b-col cols="4">
              <b-row class="day"></b-row>
            </b-col>
            <b-col cols="4"></b-col>
            <b-col cols="2"></b-col>
          </b-row>
          <b-row class="lbl">
            <b-col>
              <span>Do you want to tour with the same agent?</span>
            </b-col>
          </b-row>
          <b-row class="tournow" @click="tournowclicked()">
            <b-row class="theme-font-semi-bold justify-content-center"
              >Confirm tour</b-row
            >
          </b-row>
        </b-col>
        <b-col cols="1"></b-col>
      </b-row>
      <b-row
        v-show="step === 1"
        class="agentfound theme-font-light justify-content-center"
      >
        <b-col cols="1"></b-col>
        <b-col cols="10">
          <b-row
            style="margin-top:15px !important;margin-bottom:20px !important;"
          >
            <b-col>
              <span
                class="darkcolor theme-font-semi-bold"
                style="font-size:20px;"
                >Thanks, Jarett!</span
              >
            </b-col>
          </b-row>

          <b-row>
            <b-col>
              <span class="darkcolor theme-font-light" style="font-size:14px;"
                >Your tour has been scheduled. We will send you a reminder a few
                hours before your meeting. Thank you.</span
              >
            </b-col>
          </b-row>
          <b-row
            style="margin-top:10px !important;margin-bottom:10px !important;"
          >
            <b-col>
              <span
                class="theme-font-semi-bold graycolor"
                style="font-size:14px;"
                >Your agent</span
              >
            </b-col>
          </b-row>
          <b-row>
            <b-col cols="3">
              <img
                src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/e1/Aziz_Ansari_2012_Shankbone.JPG/1200px-Aziz_Ansari_2012_Shankbone.JPG"
                class="aimg"
                width="84"
                height="84"
              />
            </b-col>
            <b-col cols="9">
              <b-row>
                <b-col>
                  <span
                    class="theme-font-semi-bold graycolor"
                    style="font-size:17px;"
                    >Tom Haverford</span
                  >
                </b-col>
              </b-row>
              <b-row>
                <b-col>
                  <span
                    class="theme-font-regular graycolor"
                    style="font-size:12px;"
                    >Windermere</span
                  >
                </b-col>
              </b-row>
              <b-row>
                <b-col class="theme-font-light" cols="6">
                  <img src="@/assets/icons/star.svg" width="25" height="25" />
                  <span class="stars">4.9 stars</span>
                </b-col>
                <b-col>
                  <span class="tours theme-font-light">27 Nexme tours</span>
                </b-col>
              </b-row>
            </b-col>
          </b-row>

          <b-row class="ld theme-font-light">
            <b-row>
              <b-col>
                <span class="ldlbl">Listing price:</span>
              </b-col>
              <b-col>
                <span class="secondarycolor right ldlbl2"
                  >${{ numberWithCommas(listing.listingPrice) }}</span
                >
              </b-col>
            </b-row>
            <b-row>
              <b-col>
                <span class="ldlbl">Property type:</span>
              </b-col>
              <b-col>
                <span class="ldlbl2 graycolor right">{{ listing.type }}</span>
              </b-col>
            </b-row>
            <b-row>
              <b-col>
                <span class="ldlbl">Bedrooms:</span>
              </b-col>
              <b-col>
                <span class="ldlbl2 graycolor right">{{
                  listing.bedrooms
                }}</span>
              </b-col>
            </b-row>
            <b-row>
              <b-col>
                <span class="ldlbl">Square feet:</span>
              </b-col>
              <b-col>
                <span class="ldlbl2 graycolor right"
                  >{{ listing.totalSquareFoot }} sq ft</span
                >
              </b-col>
            </b-row>
            <b-row>
              <b-col>
                <span class="ldlbl">Monthly HOA:</span>
              </b-col>
              <b-col>
                <span class="ldlbl2 graycolor right">${{ listing.hoa }}</span>
              </b-col>
            </b-row>
            <b-row>
              <b-col>
                <span class="ldlbl">Year built:</span>
              </b-col>
              <b-col>
                <span class="ldlbl2 graycolor right">{{
                  listing.yearBuilt
                }}</span>
              </b-col>
            </b-row>
          </b-row>
          <b-row>
            <b-row class="theme-font-light cancel">
              <b-col>
                <span>
                  Change your mind?&nbsp;
                  <span
                    class="darkcolor"
                    style="cursor:pointer;"
                    @click="cancel()"
                    >Cancel your tour</span
                  >
                </span>
              </b-col>
            </b-row>
          </b-row>

          <b-row class="tournow" @click="returntolisting()">
            <b-col>
              <b-row class="theme-font-semi-bold justify-content-center"
                >Return to listing</b-row
              >
            </b-col>
          </b-row>
        </b-col>
        <b-col cols="1"></b-col>
      </b-row>
    </b-row>
  </b-row>
</template>

<script>
export default {
  name: "ScheduleTourWidget",
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
      step: 0
    };
  },
  methods: {
    tournowclicked() {
      this.step++;
    },
    returntolisting() {
      this.step--;
    },
    cancel() {
      this.step--;
    },
    numberWithCommas(x) {
      return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    }
  }
};
</script>
