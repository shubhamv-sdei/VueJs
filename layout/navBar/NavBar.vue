<template>
  <header id="head-top-menu" class="top-menu" :class="currentRouteName">
    <b-alert
      show
      dismissible
      variant="secondary"
      class="main-notice"
      @dismissed="hideAlert()"
    >
      <div class="container">
        <div class="row">
          <div class="col-md-12 font-18 fw-light">
            <b>COVID-19 UPDATE:</b> We are taking all necessary measures to
            ensure the health and safety of our communities, including updates
            to our home buying and selling procedures. Nexme agent partners can
            provide home tours via video chat, and for everyone’s safety, we now
            conduct open houses through live stream.
          </div>
        </div>
      </div>
    </b-alert>
    <div class="nav-container">
      <b-navbar toggleable="lg" type="dark" class="header-menu">
        <div class="container">
          <button class="navbar-toggler" type="button" @click="showmenu()">
            <svg
              class="menu-icon"
              :class="showmobilemenu ? 'active' : ''"
              viewBox="0 0 100 100"
              width="60"
              onclick="this.classList.toggle('active')"
            >
              <path
                class="line top"
                d="m 70,33 h -40 c 0,0 -6,1.368796 -6,8.5 0,7.131204 6,8.5013 6,8.5013 l 20,-0.0013"
              />
              <path class="line middle" d="m 70,50 h -40" />
              <path
                class="line bottom"
                d="m 69.575405,67.073826 h -40 c -5.592752,0 -6.873604,-9.348582 1.371031,-9.348582 8.244634,0 19.053564,21.797129 19.053564,12.274756 l 0,-40"
              />
            </svg>
          </button>
          <div class="logo-container d-flex justify-content-start">
            <nuxt-link to="/" class="navbar-brand">
              <img
                src="~/assets/images/nexme_logo.svg"
                width="100%"
                height="100%"
              />
            </nuxt-link>
          </div>
          <div class="form-inline">
            <SearchInputs
              v-if="
                pageNameVar == 'listings' ||
                  currentRouteName == 'home-state-mls'
              "
              class="singleSearchComp"
              :show-location-icon="showLocationIcon"
              :show-search="showSearch"
              :placeholder-text="placeholderText"
              :submit-text="submitText"
              :input-type="inputType"
            />
          </div>

          <!-- <b-navbar-toggle target="nav-collapse"></b-navbar-toggle> -->
          <b-collapse id="nav-collapse" is-nav>
            <b-navbar-nav class="ml-auto align-items-center">
              <b-nav-item class="top-nav-lnk mn-index" to="/" exact
                >Buy</b-nav-item
              >
              <b-nav-item
                class="top-nav-lnk mn-sell"
                :to="{ name: 'sell' }"
                exact
                >Sell</b-nav-item
              >
              <b-nav-item
                class="top-nav-lnk mn-mortgage"
                :to="{ name: 'mortgage' }"
                >Mortgage</b-nav-item
              >
              <b-nav-item
                class="top-nav-lnk mn-inspections"
                :to="{ name: 'inspections' }"
                >Inspections</b-nav-item
              >
              <b-nav-item
                :to="{ name: 'become-a-partner' }"
                class="green top-nav-lnk mn-become-a-partner"
                >Become a partner</b-nav-item
              >
              <b-nav-item
                v-if="isUser"
                class="login-menu"
                @click.prevent="launchLogin = true"
                >Sign up / Log in</b-nav-item
              >
              <b-nav-item v-else>
                <b-dropdown
                  id="dropdown-aria"
                  right
                  text="User Profile"
                  variant="transparent"
                  :no-caret="true"
                  class="m-2 header-user-avatar"
                >
                  <template v-slot:button-content class="user-name-nav">
                    <div class="d-flex align-items-center">
                      {{ userDetails.firstName }} {{ userDetails.lastName }}
                      <b-img
                        v-if="userDetails.imageUrl"
                        :src="userDetails.imageUrl"
                        v-bind="avatarConfig"
                        class="user-avatar"
                        rounded="circle"
                        alt="Avatar image"
                      ></b-img>
                      <img
                        v-else
                        class="user-avatar"
                        src="~/assets/icons/account-a.svg"
                      />
                    </div>
                  </template>
                  <b-dropdown-item
                    id="dropdown-header-1"
                    class="header-dropdown-item"
                  >
                    <div class="d-flex align-items-center justify-content-end">
                      <div
                        class="d-flex flex-column align-items-end mr-2 w-100"
                      >
                        <span id="userNameShow" class="fs-16 h3 text-right">
                          {{ userDetails.firstName }} {{ userDetails.lastName }}
                        </span>
                        <b-tooltip target="userNameShow" variant="light"
                          >{{ userDetails.firstName }}
                          {{ userDetails.lastName }}</b-tooltip
                        >
                        <span
                          class="fs-11 text-muted signuot-nav"
                          @click="signOut"
                          >Sign out</span
                        >
                      </div>
                      <b-img
                        v-if="userDetails.imageUrl"
                        :src="userDetails.imageUrl"
                        v-bind="avatarConfig"
                        rounded="circle"
                        class="user-avatar"
                        alt="Avatar image"
                      ></b-img>
                      <img
                        v-else
                        class="user-avatar"
                        src="~/assets/icons/account-a.svg"
                      />
                    </div>
                  </b-dropdown-item>
                  <b-dropdown-item
                    :to="{ name: 'account' }"
                    aria-describedby="account"
                  >
                    <span class="fs-12">Edit my profile</span>
                  </b-dropdown-item>
                  <!-- <b-nav-item
                    :to="{ name: 'sell' }"
                    aria-describedby="user-menu"
                  >
                    <span class="fs-12 font-weight-bold">Sell a home</span>
                  </b-nav-item> -->
                  <!-- <b-nav-item to="#" aria-describedby="user-menu">
                    <span class="fs-12 font-weight-bold">Rating</span>
                  </b-nav-item> -->
                  <b-dropdown-item
                    :to="{ name: 'account-favorites' }"
                    aria-describedby="account-favorites"
                    exact
                  >
                    <span class="fs-12">Favorites</span>
                  </b-dropdown-item>
                  <b-dropdown-item
                    v-if="!isAgent"
                    :to="{ name: 'become-a-partner' }"
                    aria-describedby="user-menu"
                  >
                    <span class="fs-12">Become a partner</span>
                  </b-dropdown-item>
                  <b-dropdown-divider></b-dropdown-divider>
                  <b-dropdown-item
                    :to="{ name: 'terms-of-use' }"
                    aria-describedby="user-menu"
                    exact
                  >
                    <span class="fs-12">Terms of service</span>
                  </b-dropdown-item>
                  <b-dropdown-item
                    :to="{ name: 'privacy-policy' }"
                    aria-describedby="user-menu"
                    exact
                  >
                    <span class="fs-12">Privacy policy</span>
                  </b-dropdown-item>
                  <b-dropdown-item
                    :to="{ name: 'about' }"
                    aria-describedby="user-menu"
                    exact
                  >
                    <span class="fs-12">About</span>
                  </b-dropdown-item>
                </b-dropdown>
              </b-nav-item>
              <span class="nav-indicator"></span>
            </b-navbar-nav>
          </b-collapse>
        </div>
      </b-navbar>
    </div>

    <div v-if="showmobilemenu" class="mobilemenu d-lg-none d-xl-none">
      <b-navbar-brand href="/" @click="HideMenu()">
        <!-- <img src="~/assets/logo.svg" width="100%" height="100%" /> -->
      </b-navbar-brand>
      <transition name="slide">
        <b-navbar-nav class="ml-auto">
          <b-nav-item to="/" exact @click="HideMenu()">Buy</b-nav-item>
          <b-nav-item :to="{ name: 'sell' }" exact @click="HideMenu()"
            >Sell</b-nav-item
          >
          <b-nav-item :to="{ name: 'mortgage' }" exact @click="HideMenu()"
            >Mortgage</b-nav-item
          >
          <b-nav-item :to="{ name: 'inspections' }" exact @click="HideMenu()"
            >Inspections</b-nav-item
          >
          <b-nav-item
            :to="{ name: 'become-a-partner' }"
            exact
            @click="HideMenu()"
            >Become a partner</b-nav-item
          >
          <b-nav-item
            v-if="isUser"
            @click.prevent="
              launchLogin = true;
              HideMenu();
            "
            >Sign up / Log in</b-nav-item
          >
          <b-dropdown-divider v-if="!isUser"></b-dropdown-divider>
          <b-nav-item
            v-if="!isUser"
            :to="{ name: 'account' }"
            aria-describedby="user-menu"
            @click="HideMenu()"
          >
            <span class="font-weight-bold">Edit my profile</span>
          </b-nav-item>
          <b-nav-item
            v-if="!isUser"
            :to="{ name: 'account-favorites' }"
            aria-describedby="account-favorites"
            @click="HideMenu()"
          >
            <span class="font-weight-bold">Favorites</span>
          </b-nav-item>
          <b-nav-item
            v-if="false"
            to="#"
            aria-describedby="user-menu"
            @click="HideMenu()"
          >
            <span class="font-weight-bold">Rating</span>
          </b-nav-item>
          <b-nav-item
            v-if="false"
            to="#"
            aria-describedby="user-menu"
            @click="HideMenu()"
          >
            <span class="font-weight-bold">Settings</span>
          </b-nav-item>
          <b-nav-item
            v-if="!isUser"
            :to="{ name: 'terms-of-use' }"
            aria-describedby="user-menu"
            @click="HideMenu()"
          >
            <span class="font-weight-bold">Terms of service</span>
          </b-nav-item>
          <b-nav-item
            v-if="!isUser"
            :to="{ name: 'privacy-policy' }"
            aria-describedby="user-menu"
            @click="HideMenu()"
          >
            <span class="font-weight-bold">Privacy policy</span>
          </b-nav-item>
          <b-nav-item
            v-if="!isUser"
            :to="{ name: 'about' }"
            aria-describedby="user-menu"
            @click="HideMenu()"
          >
            <span class="font-weight-bold">About</span>
          </b-nav-item>
        </b-navbar-nav>
      </transition>
      <div v-if="!isUser" class="mobile-user-avatar">
        <div class="d-flex align-items-start">
          <div class="user-name">
            {{ userDetails.firstName }} {{ userDetails.lastName }}
          </div>
          <b-img
            v-if="userDetails.imageUrl"
            :src="userDetails.imageUrl"
            v-bind="avatarConfig"
            class="user-avatar"
            rounded="circle"
            alt="Avatar image"
          ></b-img>
          <img v-else class="user-avatar" src="~/assets/icons/account-a.svg" />
        </div>
        <div class="fs-11 text-muted signuot-nav pr-5" @click="signOut">
          Sign out
        </div>
      </div>
    </div>
    <startLogin
      v-if="launchLogin"
      :start-login-box="launchLogin"
      @showLogin="unmountComp"
    ></startLogin>
  </header>
</template>
<script>
import axios from "axios";
import startLogin from "@/components/layout/login/loginModals";
import SearchInputs from "@/components/SearchInputs";
// import { Helper } from "@/plugins/mixins/helper";
export default {
  name: "NavBar",
  components: {
    startLogin,
    SearchInputs
  },
  data() {
    return {
      selectedIndex: 0,
      showmobilemenu: false,
      activeClass: "active",
      launchLogin: false,
      userDetails: {},
      isUser: true,
      isAgent: false,
      isUserloingIn: this.$store.getters.getUserToken,
      activateHeader: false,
      avatarConfig: {
        width: 28,
        height: 28,
        class: "ml-2"
      },
      showLocationIcon: true,
      submitText: "",
      text: "",
      showSearch: true,
      placeholderText: "Search by Address, City, Zip code, MLS",
      inputType: "gmapAuto",
      pageNameVar: "",
      showAlertBox: true
    };
  },
  computed: {
    currentPage() {
      return this.$route.path;
    },
    currentRouteName() {
      return this.$route.name;
    },
    getDetails() {
      return this.$store.getters.getUserInfo;
    },
    pageName() {
      return this.getRouteName();
    },
    placeHolder() {
      return this.$store.getters.getFormattedAddress;
    },
    checkListingPage() {
      return this.$store.getters.getIsListingPage;
    }
  },
  watch: {
    $route() {
      if (this.isUser) {
        this.getDetail();
      }
      this.showAlertBoxMtd();
      this.menuIndicator(this.$route.name);
    },
    getDetails(data) {
      this.isAgent = data.isAgent;
      if (this.$store.getters.getUserLoginStatus === false) {
        this.getDetail();
      }
    },
    currentRouteName() {
      if (this.$route.name === "verify-email") {
        this.activateHeader = false;
      } else {
        this.activateHeader = true;
      }
    },
    placeHolder(d) {
      this.placeholderText = d;
    },
    checkListingPage(d) {
      this.getRouteName(d);
    }
  },
  mounted() {
    this.showAlertBoxMtd();
    this.launchLogin = false;
    this.menuIndicator(this.$route.name);
    window.addEventListener("scroll", this.handleScroll);
    const userToken = localStorage.getItem("userToken");
    if (userToken !== null) {
      this.$store.commit("setUserToken", userToken);
      this.getDetail();
    }

    if (this.$store.getters.getUserInfo === {}) {
      this.getDetail();
    }

    const phText = localStorage.getItem("formattedAddress");
    if (phText !== null) {
      this.placeholderText = phText;
    }
  },
  methods: {
    showAlertBoxMtd() {
      if (this.showAlertBox) {
        const appDiv = document.getElementById("app");
        appDiv.classList.add("show-alert-box");
      }
    },
    menuIndicator(m) {
      const items = document.querySelectorAll(".top-nav-lnk");
      const cMn = document.querySelectorAll(".mn-" + m);
      const mnuLst = [
        "mn-index",
        "mn-sell",
        "mn-mortgage",
        "mn-inspections",
        "mn-become-a-partner"
      ];

      const indicator = document.querySelector(".nav-indicator");
      const handleIndicator = (e) => {
        indicator.style.width = e.offsetWidth + "px";
        indicator.style.transform = "translateX(" + e.offsetLeft + "px)";
      };

      if (mnuLst.includes("mn-" + m)) {
        handleIndicator(cMn[0]);
      }

      if (m.includes("become-a-partner")) {
        const agMn = document.querySelectorAll(".mn-become-a-partner");
        handleIndicator(agMn[0]);
      }

      if (!m.includes("become-a-partner") && !mnuLst.includes("mn-" + m)) {
        indicator.style.display = "none";
      } else {
        indicator.style.display = "block";
      }
      items.forEach((item) => {
        if (item.classList.contains("activated-menu")) {
          item.classList.remove("activated-menu");
        }
      });
    },
    hideAlert() {
      this.showAlertBox = false;
      const app = document.getElementById("app");
      app.classList.remove("show-alert-box");
    },
    unmountComp() {
      this.launchLogin = false;
    },
    getRouteName(d) {
      const routNm = this.$route.name;
      const $this = this;
      if (d) {
        $this.pageNameVar = routNm;
      } else {
        $this.pageNameVar = "";
      }
    },
    handleScroll(event) {
      const menu = document.getElementById("head-top-menu");
      const bodyRect = document.body.getBoundingClientRect();
      if (bodyRect.top > -83) {
        menu.classList.remove("menu-active");
      } else {
        menu.classList.add("menu-active");
      }
    },
    setIndex(index) {
      this.showmobilemenu = false;
      this.selectedIndex = index;
    },
    showmenu() {
      this.showmobilemenu = !this.showmobilemenu;
    },
    HideMenu() {
      this.showmobilemenu = false;
    },
    getDetail() {
      const This = this;
      const URL = this.$apiPath("main", "Account/Details", "");
      if (localStorage.getItem("userToken") !== null) {
        axios({
          method: "GET",
          url: URL,
          headers: {
            "Content-Type": "application/json",
            "X-Client-Token": This.$store.getters.getClientToken,
            "X-User-Token": localStorage.getItem("userToken")
          }
        }).then(
          (result) => {
            This.userDetails = result.data.data;
            This.$store.commit("setUserInfo", result.data.data);
            This.$store.commit("setUserLoginStatus", true);
            This.isUser = false;
          },
          (error) => {
            console.error(error.response);
            This.isUser = true;
          }
        );
      }
    },
    signOut() {
      this.isUser = false;
      this.launchLogin = false;
      localStorage.removeItem("userToken");
      localStorage.removeItem("loginDetails");
      localStorage.removeItem("favoriteList");
      if (localStorage.getItem("sendToLender") !== null) {
        localStorage.removeItem("sendToLender");
      }
      if (localStorage.getItem("tempAgentSignUpToken") !== null) {
        localStorage.removeItem("tempAgentSignUpToken");
      }
      if (localStorage.getItem("mobileCode") !== null) {
        localStorage.removeItem("mobileCode");
      }
      if (localStorage.getItem("formatted_address") !== null) {
        localStorage.removeItem("formatted_address");
      }
      if (localStorage.getItem("phoneNumber") !== null) {
        localStorage.removeItem("phoneNumber");
      }
      if (localStorage.getItem("searchedCity") !== null) {
        localStorage.removeItem("searchedCity");
      }
      if (localStorage.getItem("verifyData") !== null) {
        localStorage.removeItem("verifyData");
      }
      if (localStorage.getItem("selectedListing") !== null) {
        localStorage.removeItem("selectedListing");
      }
      location.reload();
    }
  }
};
</script>
