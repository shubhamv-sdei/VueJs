<template>
  <div>
    <b-modal ref="errorModal" hide-footer centered title="Error">
      <div class="d-block text-left">
        <h3 class="font-14 lh-30 color-dark-grey">{{ errorMsg }}</h3>
      </div>
      <div class="row">
        <div class="col-md-12 pr-0">
          <b-button class="mt-3 minsize" @click="hideModal">
            Close
          </b-button>
        </div>
      </div>
    </b-modal>

    <PhoneCode
      v-if="enterVerificationCode"
      :phone="phone"
      :error-msg="errorMsg"
      @onclose="onclose"
      @onnext="verifyCode"
    />

    <SignupDetails v-if="showSignup" @onclose="onclose" @onnext="onsignup" />
    <!-- <b-modal ref="errorModal" hide-footer centered title="Error">
      <div class="d-block text-center">
        <h3 class="error-message-2">{{ errorMsg }}</h3>
      </div>
      <b-button class="mt-3" @click="hideModal">
        Close
      </b-button>
    </b-modal> -->

    <b-modal id="loginBox" ref="loginModal" size="lg" hide-footer centered>
      <div class="d-block loginModalBox">
        <h3 class="font-24 fw-regular color-dark-green">
          Sign up / Log in
        </h3>
        <p class="color-grey-b font-16">
          Start by entering your mobile number
        </p>
        <form class="getStartedPhoneCheck" @submit.prevent="login()">
          <div class="phoneNumberCheck inpC vue-tel-input form-control">
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
              placeholder="Enter your mobile number"
              autocomplete="off"
              name="phone"
              maxlength="14"
              tabindex="0"
              class="vti__input phoneNumberLg"
              @keyup="addBracket($event)"
            />
          </div>
          <div class="error-message font-13 fw-400">
            {{ errorMsg ? errorMsg : "" }}
          </div>
          <div
            class="submit align-self-end"
            style="max-width:189px"
            align-v="center"
          >
            <button
              type="submit"
              class="btn btn-primary"
              :disabled="sendingRequest"
            >
              <span
                class="theme-font-semi-bold font-13 sImg justify-content-center minsize"
                >Next</span
              >
            </button>
          </div>
        </form>
      </div>
    </b-modal>
  </div>
</template>

<script>
import axios from "axios";
import { mapMutations } from "vuex";
import { Helper } from "@/plugins/mixins/helper";
import PhoneCode from "@/components/PhoneCode";
import SignupDetails from "@/components/SignupDetails";

export default {
  name: "LoginModals",
  components: {
    PhoneCode,
    SignupDetails
  },
  mixins: [Helper],
  props: {
    startLogin: Boolean
  },
  data() {
    return {
      enterPhoneNumber: true,
      sendingRequest: false,
      enterVerificationCode: false,
      showSignup: false,
      phone: "",
      verificationCode: "",
      formattedPhoneNumber: "",
      countryCode: 1,
      flag: "us",
      loginToken: "",
      loginInfo: {},
      userDetails: {},
      sendForCode: false,
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
        preferredCountries: ["US", "CA", "MX"],
        autocomplete: "off",
        name: "phone",
        maxLen: 10,
        inputClasses: "phoneNumberLg"
      },
      errorMsg: ""
    };
  },
  mounted() {
    this.$on("show-login", this.showlogin());
    const $this = this;
    this.$root.$on("bv::modal::hide", () => {
      if (!$this.sendForCode) {
        $this.unmountCap();
        // location.reload();
      }
      //   console.log("Modal is about to be hide", bvEvent, modalId);
    });
  },
  methods: {
    ...mapMutations(["setLoginDetails"]),
    changeCountry(flag, code) {
      this.countryCode = code;
      this.flag = flag;
      this.$store.commit("setCountryCode", code);
    },
    setCountryCode() {
      this.$store.commit("setCountryCode", this.countryCode);
    },
    login() {
      localStorage.setItem("phoneNumber", this.phone);
      const This = this;
      const phoneNumber = this.phone.replace(/[^\d]/g, "");
      this.formattedPhoneNumber = phoneNumber;
      this.sendingRequest = true;
      const URL = this.$apiPath(
        "main",
        "Account/SendCodeForPhoneAuth",
        "?phone=" + phoneNumber + "&country=" + this.countryCode
      );

      axios({
        method: "POST",
        url: URL,
        data: { phone: This.phone, country: 1 },
        headers: {
          "Content-Type": "application/json",
          "X-Client-Token": This.$store.getters.getClientToken
        }
      })
        .then(function(res) {
          console.log(res);
          This.sendingRequest = true;
          if (res.data.statusCode === "Success") {
            This.loginInfo = {
              formattedPhoneNumber: This.phone,
              challengeIdentifier: res.data.data.challengeIdentifier,
              isNewUser: res.data.data.isNewUser
            };

            This.enterVerificationCode = true;
            This.sendForCode = true;
            This.$refs.loginModal.hide();
            This.errorMsg = "";
          } else if (
            res.data.statusCode ===
            "DoS protection: User has requested too many tokens in the last hour."
          ) {
            console.log("Error entered");
            // This.$refs["errorModal"].show();
            This.errorMsg =
              "You’re doing that too much, and have been locked out. Please try again in 24 hours.";
          } else {
            // This.$refs["errorModal"].show();
            This.errorMsg =
              "You’re doing that too much, and have been locked out. Please try again in 24 hours.";
          }
        })
        .catch((error) => {
          throw new Error(error);
        });
    },
    verifyCode(code) {
      const This = this;
      const loginInfo = This.loginInfo;
      const URL = this.$apiPath(
        "main",
        "Account/VerifyCodeForPhoneAuth",
        `?challengeidentifier=${loginInfo.challengeIdentifier}&accesstoken=${code}`
      );

      axios({
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          "X-Client-Token": This.$store.getters.getClientToken
        },
        url: URL
      })
        .then(function(res) {
          console.log(res.data);
          if (res.data.statusCode === "Success") {
            This.enterVerificationCode = false;
            This.loginToken = res.data.data.id;
            if (This.loginInfo.isNewUser) {
              This.showSignup = true;
            } else {
              This.requestLogin(res.data.data.id);
            }
          } else if (res.data.errorMessage === "Exception:Token is invalid") {
            This.errorMsg =
              "Invalid code, please enter the correct 6-digit code.";
          } else if (
            res.data.errorMessage ===
            "DoS protection: User has requested too many tokens in the last hour."
          ) {
            This.errorMsg =
              "Unfortunately, you're locked out. Please try again after 1 hours.";
          } else if (
            res.data.errorMessage.indexxOf(
              "Account suspended: too many attempts."
            ) > -1
          ) {
            This.errorMsg =
              "You’re doing that too much, and have been locked out. Please try again in 24 hours.";
          } else {
            this.errorMsg =
              "Your code has expired. Please request another verification code.";
          }
        })
        .catch((error) => {
          throw new Error(error);
        });
    },
    requestLogin(code) {
      const URL = this.$apiPath("main", "Account/LoginWithPhone", "");
      const This = this;

      const Data = {
        accessToken: code,
        deviceToken: ""
      };
      if (This.userDetails !== undefined) {
        Data.user = {
          email: This.userDetails.email,
          firstName: This.userDetails.firstName,
          lastName: This.userDetails.lastName,
          phone: This.formattedPhoneNumber
        };
      } else {
        Data.user = {
          phone: This.formattedPhoneNumber
        };
      }

      axios({
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          "X-Client-Token": This.$store.getters.getClientToken
        },
        url: URL,
        data: Data
      })
        .then(function(res) {
          console.log(res.data);
          if (res.data.statusCode === "Success") {
            localStorage.setItem("userToken", res.data.data.id);
            const obj = Object.assign(Data, res.data.data);
            This.$store.commit("setLoginDetails", obj);
            localStorage.setItem("loginDetails", JSON.stringify(obj));
            if (res.data.data.user.isAgent === true) {
              This.$store.commit("setUserToken", res.data.data.id);

              location.reload();
            } else {
              location.reload();
            }
            This.$store.commit("setUserToken", res.data.data.id);
          } else {
            This.errorMsg = "error";
          }
        })
        .catch((error) => {
          throw new Error(error);
        });
    },
    onclose() {
      this.phone = "";
      this.errorMsg = "";
      this.showSignup = false;
      this.enterVerificationCode = false;
      this.enterPhoneNumber = true;
      this.unmountCap();
    },
    onnext() {
      this.phone = "";
      this.errorMsg = "";
      this.showSignup = true;
    },
    onsignup(user) {
      this.showSignup = false;
      this.userDetails = user;
      this.requestLogin(this.loginToken);
    },
    checkInputValue($evt) {
      if ($evt.target.value === "") {
        this.searchButton = true;
      } else {
        this.searchButton = false;
      }
    },
    addBracket(e) {
      const phone = e.target;
      if (phone.value.length === 0) {
        phone.value = "(" + phone.value;
      }
      this.checkInputValue(e);
    },
    showModal() {
      this.$refs.errorModal.show();
    },
    hideModal() {
      this.$refs.errorModal.hide();
      this.errorMsg = "";
    },
    showlogin() {
      this.$refs.loginModal.show();
    },
    unmountCap() {
      this.$emit("showLogin", "login");
    }
  }
};
</script>
