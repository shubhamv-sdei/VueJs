<template>
  <footer class="footer">
    <div class="container">
      <b-row>
        <b-col sm="12" md="12" lg="12" xl="4" class="footerColumns about-col">
          <!-- Nexme logo section-->
          <b-row>
            <a href="/">
              <img src="~/assets/logo.svg" width="177" height="27" />
            </a>
          </b-row>
          <b-row
            class="aboutnexmetext theme-font-regular footer-about"
            align-v="center"
            ><div class="col-md-10">
              Nexme is an on demand real estate platform that delivers instant
              access to homes and full-service, professional real estate agents.
              All at the click of a button.
            </div></b-row
          >
          <b-row class="d-pt-30 m-pt-20">
            <b-col cols="2" sm="1" class="pl-0 pr-0">
              <a href="https://www.facebook.com/NEXMEapp" target="_blank">
                <img src="~/assets/icons/f-button.svg" width="26" height="26" />
              </a>
            </b-col>
            <b-col cols="2" sm="1" class="pl-0 pr-0">
              <a href="https://twitter.com/nexmeapp" target="_blank">
                <img src="~/assets/icons/t-button.svg" width="26" height="26" />
              </a>
            </b-col>
            <b-col cols="2" sm="1" class="pl-0 pr-0">
              <a href="https://www.instagram.com/nexmeapp/" target="_blank">
                <img src="~/assets/icons/i-button.svg" width="26" height="26" />
              </a>
            </b-col>
            <b-col cols="2" sm="1" class="pl-0 pr-0">
              <a href="https://www.linkedin.com/company/nexme/" target="_blank">
                <img src="~/assets/icons/l-button.svg" width="26" height="26" />
              </a>
            </b-col>
            <b-col cols="12" sm="6" md="8" class="corp">
              <b-row class="corptext theme-font-regular fix-space">
                <nuxt-link :to="{ name: 'privacy-policy' }"
                  >Privacy Policy</nuxt-link
                >&emsp;|&emsp;<nuxt-link :to="{ name: 'terms-of-use' }"
                  >Terms of Use</nuxt-link
                >
              </b-row>
              <b-row class="corptext theme-font-regular">
                <a href="http://nexme.com/">
                  Nexme {{ new Date().getFullYear() }}
                </a>
              </b-row>
            </b-col>
          </b-row>
        </b-col>

        <b-col sm="12" md="12" lg="4" xl="2" class="d-sm-block footerColumns">
          <!-- Our app section-->
          <div class="footerItemHeader theme-font-semi-bold">
            <h5 class="footer-header"><span>Our app</span></h5>
          </div>
          <ul class="nav flex-column footer-links">
            <li class="nav-item mt-3">
              <a class="app-link" :href="appleUrl" target="_blank">
                <img
                  src="~/assets/images/apple_dn.png"
                  alt="Nexme IOS app download"
                  width="196"
                />
              </a>
            </li>
            <li class="nav-item pt-2">
              <a class="app-link" :href="googleUrl" target="_blank">
                <img
                  src="~/assets/images/google_dn.png"
                  class="img-fluid"
                  alt="Nexme android app download"
                  width="196"
                />
              </a>
            </li>
          </ul>
        </b-col>
        <b-col sm="12" md="12" lg="4" xl="2" class="d-sm-block footerColumns">
          <!-- Features section-->
          <div class="footerItemHeader theme-font-semi-bold">
            <h5 class="footer-header"><span>Features</span></h5>
          </div>
          <ul class="nav flex-column footer-links">
            <li class="nav-item">
              <nuxt-link to="/">Buy</nuxt-link>
            </li>
            <li class="nav-item">
              <nuxt-link :to="{ name: 'sell' }">Sell</nuxt-link>
            </li>
            <li class="nav-item">
              <nuxt-link :to="{ name: 'mortgage' }">Mortgage</nuxt-link>
            </li>
            <li class="nav-item">
              <nuxt-link :to="{ name: 'inspections' }">
                Inspection
              </nuxt-link>
            </li>
          </ul>
        </b-col>
        <b-col sm="12" md="12" lg="12" xl="2" class="footerColumns">
          <div class="footerItemHeader theme-font-semi-bold">
            <h5 class="footer-header"><span>Partners</span></h5>
          </div>
          <ul class="nav flex-column footer-links">
            <li class="nav-item">
              <nuxt-link :to="{ name: 'become-a-partner-agent' }">
                Agent
              </nuxt-link>
            </li>
            <li class="nav-item">
              <nuxt-link :to="{ name: 'become-a-partner-lender' }">
                Lender
              </nuxt-link>
            </li>
            <li class="nav-item">
              <nuxt-link :to="{ name: 'become-a-partner-inspector' }">
                Inspector
              </nuxt-link>
            </li>
            <li class="nav-item">
              <nuxt-link :to="{ name: 'become-a-partner-builder' }">
                Builder
              </nuxt-link>
            </li>
            <li class="nav-item">
              <nuxt-link :to="{ name: 'become-a-partner-title-and-escrow' }">
                Title & Escrow
              </nuxt-link>
            </li>
          </ul>
        </b-col>
        <b-col sm="12" md="12" lg="4" xl="2" class="d-sm-block footerColumns">
          <!-- Company section-->
          <div class="footerItemHeader theme-font-semi-bold">
            <h5 class="footer-header"><span>Company</span></h5>
          </div>
          <ul class="nav flex-column footer-links">
            <li class="nav-item">
              <nuxt-link :to="{ name: 'about' }">About Nexme</nuxt-link>
            </li>
            <li class="nav-item">
              <nuxt-link :to="{ name: 'mission' }">Our Mission</nuxt-link>
            </li>
            <li class="nav-item">
              <nuxt-link :to="{ name: 'blog' }">Blog</nuxt-link>
            </li>
            <li class="nav-item">
              <nuxt-link to="/careers">Careers</nuxt-link>
            </li>
            <!-- <li class="nav-item">
                <nuxt-link to="/contact">Contact</nuxt-link>
              </li> -->
            <li class="nav-item">
              <a href="mailto:contact@nexme.com">
                contact@nexme.com
              </a>
            </li>
            <li class="nav-item">
              <a href="tel:1-800-390-8801">
                1-800-390-8801 <span class="text-wrap">(6am-7pm PT)</span>
              </a>
            </li>
          </ul>
        </b-col>
      </b-row>
    </div>
  </footer>
</template>

<script>
export default {
  name: "Footer",
  data() {
    return {
      activateFooter: false
    };
  },
  computed: {
    currentRouteName() {
      return this.$route.name;
    },
    appleUrl() {
      return this.$store.getters.getAppleUrl;
    },
    googleUrl() {
      return this.$store.getters.getGoogleUrl;
    }
  },
  watch: {
    currentRouteName() {
      if (
        this.$route.name === "listings" &&
        this.$route.name === "verify-email"
      ) {
        this.activateFooter = false;
      } else {
        this.activateFooter = true;
      }
    }
  }
};
</script>
