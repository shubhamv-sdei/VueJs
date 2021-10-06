<template>
  <div class="base-bg pb-4 visual-brand-box">
    <div class="container">
      <b-row>
        <div class="col-sm-12 col-md-12 col-xl-7 col-lg-7 pr-0 pl-0">
          <b-row>
            <div class="col-md-10 pl-0 pr-0">
              <Intro :data="intro" />
            </div>
          </b-row>
          <b-row
            v-for="(item, index) in descriptionTiles"
            :key="index"
            class="gap"
          >
            <div class="col-xl-10 col-lg-12 col-md-12 col-sm-12 pl-0">
              <ProcessDescriptionCard
                :data-obj="item"
                :selected="selectedIndex === index"
                @on-div-click="setSelectedCardIndex(index)"
              />
            </div>
          </b-row>
          <div v-if="section" class="row">
            <div class="col-md-12 col-sm-12 col-lg-12 col-xl-6 pl-0 applyBtn">
              <button
                v-if="type == 'button'"
                class="btn btn-primary btn-thik"
                @click="navigateToPage()"
              >
                {{ button }}
              </button>
              <a v-else class="btn btn-primary btn-thik" :href="link">
                {{ button }}
              </a>
            </div>
          </div>
          <b-row
            v-if="!section"
            class="gapxl app-store-icons"
            align-v="center"
            no-gutters
          >
            <div class="col-md-12 col-sm-12 col-lg-12 col-xl-6 pl-0">
              <div class="row">
                <div class="col-6 col-sm-6 pl-0 tablet-text-right">
                  <a :href="appleUrl" target="_blank">
                    <img
                      src="~/assets/images/apple_dn.png"
                      alt="Nexme IOS app download"
                      class="img-fluid store-icons"
                    />
                  </a>
                </div>
                <div class="col-6 col-sm-6 pl-0 text-left">
                  <a :href="googleUrl" target="_blank">
                    <img
                      src="@/assets/images/google_dn.png"
                      class="img-fluid store-icons"
                      alt="Nexme Android App, Google Play Store"
                    />
                  </a>
                </div>
              </div>
            </div>
          </b-row>
        </div>
        <div class="d-none col-md-4 col-xl-5 col-lg-5 d-lg-none d-xl-block">
          <img
            :src="`/assets/images/${imgsrc}`"
            width="100%"
            style="max-width: 430px;"
          />
          <b-row
            v-if="section"
            class="gapxl app-store-icons"
            align-v="center"
            no-gutters
          >
            <div class="col-md-10 col-lg-10 col-xl-9 pl-0 text-center">
              <div class="row">
                <div class="col-6 col-sm-6 pl-0 tablet-text-right">
                  <a :href="appleUrl" target="_blank">
                    <img
                      src="~/assets/images/apple_dn.png"
                      alt="Nexme IOS app download"
                      class="img-fluid store-icons"
                    />
                  </a>
                </div>
                <div class="col-6 col-sm-6 pl-0 text-left">
                  <a :href="googleUrl" target="_blank">
                    <img
                      src="@/assets/images/google_dn.png"
                      class="img-fluid store-icons"
                      alt="Nexme Android App, Google Play Store"
                    />
                  </a>
                </div>
              </div>
            </div>
          </b-row>
        </div>
      </b-row>
    </div>
    <AgentSignUp
      v-if="launchLogin"
      :start-login-box="launchLogin"
      @showLogin="unmountComp"
    />
  </div>
</template>

<script>
import ProcessDescriptionCard from "@/components/ProcessDescriptionCard";
import AgentSignUp from "@/components/AgentSignUp";
import Intro from "@/components/Intro";

export default {
  name: "VisualBrandBox",
  components: {
    ProcessDescriptionCard,
    Intro,
    AgentSignUp
  },
  props: {
    intro: {
      type: Array,
      default: () => {
        return [];
      }
    },
    descriptionTiles: {
      type: Array,
      default: () => {
        return [];
      }
    },
    imgsrc: {
      type: String,
      default: ""
    },
    button: {
      type: String,
      default: ""
    },
    link: {
      type: String,
      default: ""
    },
    section: {
      type: Boolean
    },
    type: {
      type: String,
      default: "button"
    }
  },
  data() {
    return {
      selectedIndex: 0,
      startAgentSignUp: false,
      launchLogin: false
    };
  },
  computed: {
    appleUrl() {
      return this.$store.getters.getAppleUrl;
    },
    googleUrl() {
      return this.$store.getters.getGoogleUrl;
    }
  },
  methods: {
    setSelectedCardIndex(index) {
      this.selectedIndex = index;
    },
    navigateToPage() {
      if (this.link === "agent-become-an-agent-details") {
        this.launchLogin = true;
      } else if (this.link === "disable") {
        console.log("page is not ready");
      } else {
        this.$router.push({ name: this.link });
      }
    },
    unmountComp() {
      this.launchLogin = false;
    }
  }
};
</script>
