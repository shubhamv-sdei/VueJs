<template>
  <div class="code-modal">
    <div class="layer"></div>
    <div class="phone-code-popup" no-gutters>
      <div class="box">
        <div class="a">
          <span class="theme-font-regular m-f-16 head-text">
            Enter the 6 digit code sent to
            <span class="theme-font-semi-bold head-number">
              {{ phonetext }}
            </span>
          </span>
          <p v-if="errorMsg" class="error-message-1">{{ errorMsg }}</p>
        </div>
        <div class="digits d-flex">
          <b-input
            id="one"
            ref="one"
            v-model="one"
            type="tel"
            tabindex="0"
            maxlength="6"
            min="000000"
            max="999999"
            class="sb"
            pattern="[0-9]{6}"
            placeholder="Enter code"
            :style="codeerror ? 'border-color:red;' : ' border-color: #f0f0f0;'"
            @keyup="
              keyup;
              keypress($event);
            "
          />
        </div>
        <b-row class="b">
          <b-col
            cols="6"
            class="text-center d-flex align-items-center justify-content-start"
          >
            <span
              v-show="timeleft > 0"
              class="theme-font-light code-timer"
              style="font-size:12px;color:#888888;"
              >Resend code in 0:{{
                timeleft > 9 ? "" + timeleft : "0" + timeleft
              }}</span
            >
            <a style="vertical-align: top;" @click="sendcode">
              <span
                v-show="timeleft === 0"
                class="theme-font-light text-center code-timer"
                style="font-size:12px;cursor:pointer;"
                >Send code again</span
              >
            </a>
          </b-col>
          <b-col
            cols="6"
            class="text-center d-flex align-items-center justify-content-end"
          >
            <button
              class="btn btn-primary minsize"
              type="button"
              @click="next()"
            >
              Next
            </button>
          </b-col>
        </b-row>
      </div>
      <div class="close-btn">
        <button class="close" @click="close()"></button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "PhoneCode",
  props: {
    phone: {
      type: String,
      default: ""
    },
    errorMsg: {
      type: String,
      default: ""
    }
  },
  data() {
    return {
      finalCode: "",
      one: "",
      two: "",
      three: "",
      four: "",
      five: "",
      six: "",
      codeerror: false,
      timer: null,
      timeleft: 59,
      error: this.$props.errorMsg
    };
  },
  computed: {
    phonetext() {
      const phoneNumberString = this.$props.phone;
      const cleaned = ("" + phoneNumberString).replace(/\D/g, "");
      const match = cleaned.match(/^(\d{3})(\d{3})(\d{4})$/);
      if (match) {
        return "(" + match[1] + ") " + match[2] + "-" + match[3];
      }
      return null;
    }
  },
  created() {
    this.timer = setInterval(this.timerfired, 1000);
  },
  mounted() {
    this.$refs.one.focus();
  },
  methods: {
    keypress(event) {
      if (isNaN(Number(event.target.value))) {
        event.target.value = "";
        this.one = "";
        event.preventDefault();
      } else {
        return true;
      }
    },
    keyup($event) {
      const val = $event.target.value;
      this.finalCode = val;
    },
    next() {
      if (this.one === "") {
        this.codeerror = true;
      } else {
        this.codeerror = false;
        const code = this.one;
        this.one = "";
        clearInterval(this.timer);
        this.timer = null;
        this.$emit("onnext", code);
        localStorage.setItem("mobileCode", code);
      }
    },
    close() {
      this.one = "";
      this.codeerror = false;
      clearInterval(this.timer);
      this.timer = null;
      this.$emit("onclose");
      this.$store.commit("setLoadingBcomeAgentBtn", false);
    },
    timerfired() {
      this.timeleft--;
      if (this.timeleft === 0) {
        clearInterval(this.timer);
        this.timer = null;
      }
    },
    sendcode() {
      this.one = "";
      this.$emit("onresend");
      this.timeleft = 59;
      this.timer = setInterval(this.timerfired, 1000);
    }
  }
};
</script>
