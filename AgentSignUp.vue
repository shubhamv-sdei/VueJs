<template>
  <div>
    <b-modal id="loginBox" ref="loginModal" size="md" hide-footer centered>
      <div class="d-block text-left loginModalBox">
        <h3 class="color-dark-green font-24 fw-regular">
          Create an account or sign in
        </h3>
        <p class="color-grey font-16 top-subtitle">
          Enter your mobile number to start your application
        </p>
        <form
          class="getStartedPhoneCheck"
          @submit.prevent="startBecomeAnAgent()"
        >
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
          <div class="font-11 color-dark-green fw-light">
            Free! No credit card required. Cancel any time.
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

    <PhoneCode
      v-if="showCodeScreen"
      :phone="phone"
      :error-msg="errorMsg"
      @onclose="onclose"
      @onnext="sendToSignUp"
      @onresend="resendTheCode"
    />

    <SignupDetails v-if="showSignup" @onclose="onclose" @onnext="onsignup" />

    <b-modal
      ref="errorModal"
      hide-footer
      centered
      :title="modalTitle"
      @hidden="hideModal"
    >
      <div class="d-block">
        <h3 class="font-14 color-dark-grey">{{ errorMsg }}</h3>
      </div>
      <a
        v-if="true"
        href="#"
        class="mt-3 pt-1 link font-13 float-left"
        @click.prevent="goToLogin"
      >
        Log in instead
      </a>
      <button class="mt-3 float-right btn btn-primary" @click="hideModal">
        Try again
      </button>
    </b-modal>
  </div>
</template>

<script>
import axios from "axios";
import { mapMutations } from "vuex";
import PhoneCode from "@/components/PhoneCode";
import { Helper } from "@/plugins/mixins/helper";
import SignupDetails from "@/components/SignupDetails";

export default {
  name: "AgentSignUp",
  components: {
    PhoneCode,
    SignupDetails
  },
  directives: {
    parseVal: {
      bind(el) {
        const handler = function(e) {
          const letters = /^[a-zA-Z]+$/;
          const string = e.target.value;
          letters.test(string) || !string
            ? document.getElementById("input").classList.remove("is-invalid")
            : document.getElementById("input").classList.add("is-invalid");
        };
        el.addEventListener("keyup", handler);
      }
    }
  },
  mixins: [Helper],
  data() {
    return {
      showCodeScreen: false,
      phone: "",
      errorMsg: "",
      loading: false,
      loginLink: false,
      modalTitle: "Error",
      enterPhoneNumber: true,
      sendingRequest: false,
      enterVerificationCode: false,
      showSignup: false,
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
      }
    };
  },
  mounted() {
    // this.showlogin();

    this.$on("show-login", this.showlogin());
    const $this = this;
    this.$root.$on("bv::modal::hide", () => {
      if (!$this.sendForCode) {
        $this.unmountCap();
      }
    });
  },
  methods: {
    ...mapMutations(["setLoginDetails"]),
    startBecomeAnAgent() {
      this.$store.commit("setLoadingBcomeAgentBtn", true);
      const $this = this;
      const num = this.phone;
      const PHONE = num
        .substring(1, num.length)
        .replace("(", "")
        .replace(" ", "")
        .replace(")", "")
        .replace(/-/g, "");
      this.formattedPhoneNumber = PHONE;
      const TOKEN = this.$store.state.X_CLIENT_TOKEN;
      const countryCode = this.$store.getters.getCountryCode;
      const URL = this.$apiPath(
        "main",
        "Account/SendCodeForPhoneAuth",
        "?phone=" + PHONE + "&country=" + countryCode
      );
      const errorSuspended = this.$store.state.errorSuspended;
      const DDosProtection = this.$store.state.DDosProtection;
      const usedToken = this.$store.state.usedToken;

      axios({
        method: "post",
        headers: { "X-Client-Token": TOKEN },
        url: URL,
        data: {
          phone: PHONE,
          country: 1
        }
      }).then(function(res) {
        console.log(res);
        if (res.data.statusCode === "Success") {
          $this.showCodeScreen = true;
          $this.phone = num;
          const verifyData = {
            phoneNumber: num,
            formattedPhoneNumber: PHONE,
            challengeIdentifier: res.data.data.challengeIdentifier,
            isNewUser: res.data.data.isNewUser
          };

          localStorage.setItem("verifyData", JSON.stringify(verifyData));
          localStorage.setItem("formattedPhoneNumber", PHONE);
          localStorage.setItem("phoneNumber", num);

          $this.enterVerificationCode = true;
          $this.sendForCode = true;
          $this.$refs.loginModal.hide();
          $this.showCodeScreen = true;
          $this.errorMsg = "";
          $this.$refs.errorModal.hide();
        } else if (res.data.errorMessage === DDosProtection) {
          $this.$refs.errorModal.show();
          $this.errorMsg = $this.$store.state.lockedOut;
        } else if (res.data.errorMessage.includes(errorSuspended)) {
          $this.$refs.errorModal.show();
          $this.errorMsg = res.data.errorMessage;
        } else if (res.data.errorMessage === usedToken) {
          $this.$refs.errorModal.show();
          $this.errorMsg =
            "Your 6 digit code is invalid. It was used recently.";
        } else if (res.data.errorMessage.includes("Token is invalid")) {
          $this.$refs.errorModal.show();
          $this.errorMsg =
            "Your token is expired. please request another token.";
        } else {
          $this.$refs.errorModal.show();
          $this.errorMsg = "Wrong number provided";
        }
      });
    },
    onclose() {
      this.showCodeScreen = false;
      this.unmountCap();
      this.$store.commit("setSignupError", "");
      this.errorMsg = "";
      this.$store.commit("setLoadingBcomeAgentBtn", false);
    },
    goToLogin() {
      // this.$router.push({ name: "login" });
      // const code = localStorage.getItem("mobileCode");
      this.requestLogin(this.loginToken);
    },
    async sendToSignUp(code) {
      const $this = this;
      const verifyData = localStorage.getItem("verifyData");
      const objData = JSON.parse(verifyData);
      const loginInfo = {
        phone: objData.phoneNumber,
        formattedPhoneNumber: objData.formattedPhoneNumber,
        challengeIdentifier: objData.challengeIdentifier,
        isNewUser: objData.isNewUser,
        code
      };

      const agentdata = {
        phone: loginInfo.formattedPhoneNumber,
        challengeIdentifier: loginInfo.challengeIdentifier,
        accessToken: loginInfo.code
      };

      const Url = this.$apiPath("Agent", "VerifyCodeWithAgentStatus", "");

      const lockedOut = $this.$store.state.lockedOut;
      const invalidCode = $this.$store.state.invalidCode;
      const expiredCode = $this.$store.state.expiredCode;

      await axios({
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          "X-Client-Token": $this.$store.getters.getClientToken
        },
        url: Url,
        data: agentdata
      })
        .then(function(res) {
          if (res.data.statusCode === "Success") {
            $this.errorMsg = "";
            //   $this.enterVerificationCode = false;
            $this.loginToken = res.data.data.token;
            if (res.data.data.user.isAgent) {
              if (res.data.data.user.agentStatus === 0) {
                $this.errorMsg = $this.$store.state.accountPending;
                $this.modalTitle = "Agent application under review";
                $this.showCodeScreen = false;
                $this.$refs.errorModal.show();
              } else if (res.data.data.user.agentStatus === 2) {
                $this.errorMsg = $this.$store.state.Rejected;
                $this.showCodeScreen = false;
                $this.$refs.errorModal.show();
              } else if (res.data.data.user.agentStatus === 3) {
                $this.showCodeScreen = false;
                $this.$router.push({ name: "agent-become-an-agentdetails" });
                localStorage.setItem(
                  "tempAgentSignUpToken",
                  res.data.data.token
                );
                $this.$store.commit("setLoadingBcomeAgentBtn", false);
              } else if (res.data.data.user.agentStatus === 4) {
                $this.errorMsg = $this.$store.state.AgentRemoved;
                $this.showCodeScreen = false;
                $this.$refs.errorModal.show();
              } else {
                $this.showCodeScreen = false;
                $this.errorMsg = $this.$store.state.alreadyRegistered;
                $this.showCodeScreen = false;
                $this.loginLink = true;
                $this.$refs.errorModal.show();
                // localStorage.setItem("userToken", res.data.data.token);
                // $this.$router.push({
                //   name: "buy",
                //   params: { userLoginStatus: true }
                // });
              }

              // $this.$router.push({ name: "agent-become-an-agentdetails" });
              // $this.errorMsg = $this.$store.state.lockedOut;
              // $this.$refs["errorModal"].show();
              // console.log(res.data.data.user.isAgent);
            } else if (res.data.data.user.agentStatus === -1) {
              $this.showCodeScreen = false;
              $this.$router.push({ name: "agent-become-an-agent-details" });
              localStorage.setItem("tempAgentSignUpToken", res.data.data.token);
              $this.$store.commit("setLoadingBcomeAgentBtn", false);
            } else if (res.data.data.user.agentStatus === 0) {
              $this.errorMsg = $this.$store.state.accountPending;
              $this.modalTitle = "Agent application under review";
              $this.showCodeScreen = false;
              $this.$refs.errorModal.show();
            } else if (res.data.data.user.agentStatus === 2) {
              $this.errorMsg = $this.$store.state.Rejected;
              $this.showCodeScreen = false;
              $this.$refs.errorModal.show();
            } else if (res.data.data.user.agentStatus === 3) {
              $this.showCodeScreen = false;
              $this.$router.push({ name: "agent-become-an-agent-details" });
              localStorage.setItem("tempAgentSignUpToken", res.data.data.token);
              $this.$store.commit("setLoadingBcomeAgentBtn", false);
            } else if (res.data.data.user.agentStatus === 4) {
              $this.errorMsg = $this.$store.state.AgentRemoved;
              $this.showCodeScreen = false;
              $this.$refs.errorModal.show();
            } else {
              $this.showCodeScreen = false;
              $this.errorMsg = $this.$store.state.alreadyRegistered;
              $this.showCodeScreen = false;
              $this.$refs.errorModal.show();
              // localStorage.setItem("userToken", res.data.data.token);
              // $this.$router.push({
              //   name: "buy",
              //   params: { userLoginStatus: true }
              // });
            }
            // if (loginInfo.isNewUser) {
            //   console.log(res.data);
            //   $this.showCodeScreen = false;
            //   $this.$router.push({ name: "agent-become-an-agent-details" });
            //   localStorage.setItem("tempAgentSignUpToken", res.data.data.token);
            // } else {
            //   $this.showCodeScreen = false;
            //   $this.$router.push({ name: "agent-become-an-agent-details" });
            //   // $$this.requestLogin(res.data.data.token);
            // }

            // console.log(res.data.data.token);
          } else {
            if (res.data.errorMessage.includes("Exception:Token is invalid")) {
              $this.errorMsg = invalidCode;
              $this.showCodeScreen = true;
            } else if (res.data.errorMessage.includes("DoS protection")) {
              $this.errorMsg = lockedOut;
              $this.showCodeScreen = true;
            } else if (
              res.data.errorMessage.includes("Invalid token supplied")
            ) {
              $this.errorMsg = invalidCode;
              $this.showCodeScreen = true;
            } else {
              $this.errorMsg = expiredCode;
              $this.showCodeScreen = true;
            }
            $this.errorMsg = "error";
          }
        })
        .catch((error) => {
          // throw new Error(error);
          return error;
        });
    },
    // hideModal() {
    //   this.$store.commit("setLoadingBcomeAgentBtn", false);
    //   this.$refs.errorModal.hide();
    //   this.errorMsg = "";
    // },
    scrollToTop() {
      window.scrollTo(0, 0);
    },
    resendTheCode() {
      const phone = localStorage.getItem("phoneNumber");
      this.startBecomeAnAgent(phone);
    },
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
          console.log(res.data.data);
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
            This.showCodeScreen = true;
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
    // onclose() {
    //   this.phone = "";
    //   this.errorMsg = "";
    //   this.showSignup = false;
    //   this.enterVerificationCode = false;
    //   this.enterPhoneNumber = true;
    //   this.unmountCap();
    // },
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
      this.unmountCap();
    },
    showlogin() {
      this.$refs.loginModal.show();
    },
    unmountCap() {
      const $this = this;
      setTimeout(() => {
        $this.$emit("showLogin", false);
      }, 300);
    }
  }
};
</script>
