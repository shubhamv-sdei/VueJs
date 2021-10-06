<template>
  <b-row class="confirm-tour-widget">
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
    <b-row v-show="step === 0" class="confirm justify-content-center">
      <b-row class="tournow" @click="tournowclicked()">
        <b-row class="theme-font-semi-bold justify-content-center"
          >Confirm tour</b-row
        >
      </b-row>
    </b-row>
    <b-row v-show="step === 1" class="connecting theme-font-regular">
      <b-row class="b" @click="step++">
        <b-col>
          <span>Connecting you to the nearest agent…</span>
        </b-col>
      </b-row>
      <b-row class="justify-content-end c" @click="cancel()">
        <b-col cols="9"></b-col>
        <b-col>
          <span>Cancel tour</span>
        </b-col>
      </b-row>
    </b-row>
    <b-row
      v-show="step === 2"
      class="agentfound theme-font-light justify-content-center"
    >
      <b-row>
        <b-col cols="4">
          <b-row>
            <b-col cols="1">
              <img src="@/assets/icons/star.svg" width="25" height="25" />
            </b-col>
            <b-col class="theme-font-light">
              <span class="stars">4.9 stars</span>
            </b-col>
          </b-row>
          <b-row class="justify-content-center">
            <span class="tours theme-font-regular">27 Nexme tours</span>
          </b-row>
        </b-col>
        <b-col cols="4">
          <b-row class="justify-content-center">
            <img
              src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/e1/Aziz_Ansari_2012_Shankbone.JPG/1200px-Aziz_Ansari_2012_Shankbone.JPG"
              class="a up"
              width="106"
              height="106"
            />
          </b-row>
          <b-row class="agentname theme-font-semi-bold justify-content-center">
            <span>Tom Haverford</span>
          </b-row>
          <b-row class="theme-font-light af justify-content-center">
            <span>Windermere</span>
          </b-row>
        </b-col>
        <b-col cols="4">
          <b-row class="justify-content-end">
            <span class="arr secondarycolor theme-font-light"
              >Arriving in 11 mins</span
            >
          </b-row>
          <b-row class="justify-content-end" @click="step--">
            <span class="canceltours theme-font-regular">Cancel</span>
          </b-row>
        </b-col>
      </b-row>
      <b-row class="justify-content-center theme-font-semi-bold">
        <b-col></b-col>
        <b-col>
          <b-row class="call justify-content-center">
            <span>Call</span>
          </b-row>
        </b-col>
        <b-col>
          <b-row class="msg justify-content-center">
            <span>Message</span>
          </b-row>
        </b-col>
        <b-col></b-col>
      </b-row>
      <b-row>
        <b-row class="sep"></b-row>
        <b-row>
          <b-col>
            <span class="tl theme-font-regular">Tour location</span>
          </b-col>
        </b-row>
        <b-row>
          <b-col cols="9">
            <b-row>
              <span class="theme-font-light ad"
                >{{ listing.house }}{{ listing.directionalPrefix
                }}{{ listing.street }} {{ listing.streetSuffix }}
                {{ listing.directionalSuffix }} {{ listing.unit }}</span
              >
            </b-row>
            <b-row>
              <span class="theme-font-light ad"
                >{{ listing.city }}&nbsp;{{ listing.state }}&nbsp;{{
                  listing.zip
                }}</span
              >
            </b-row>
          </b-col>
          <b-col cols="3" class="dir">
            <a
              id="dirhref"
              :href="
                'https://www.google.com/maps/search/?api=1&query=' +
                  listing.latitude +
                  ',' +
                  listing.longitude
              "
              target="_blank"
            >
              <b-row class="justify-content-center">
                <img
                  src="@/assets/icons/directions_icon.svg"
                  width="25"
                  height="21"
                />
              </b-row>
              <b-row class="justify-content-center">
                <span class="theme-font-light">Directions</span>
              </b-row>
            </a>
          </b-col>
        </b-row>
      </b-row>
    </b-row>
  </b-row>
</template>

<script>
export default {
  name: "ConfirmTourWidget",
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
    cancel() {
      this.step--;
    }
  }
};
</script>
