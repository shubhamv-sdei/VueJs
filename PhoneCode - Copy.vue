<template>
  <div class="code-modal">
    <div class="layer"></div>
    <div class="phone-code-popup" no-gutters>
      <div class="box">
        <div class="a text-center">
          <span class="theme-font-regular m-f-16 head-text">
            Enter the 6 digit code sent to
            <span class="theme-font-semi-bold head-number">
              {{ phonetext }}
            </span>
          </span>
          <p v-if="errorMsg" class="error-message-1">{{ errorMsg }}</p>
        </div>
        <div class="digits d-flex justify-content-around mx-auto">
          <b-input
            id="one"
            ref="one"
            v-model="one"
            type="number"
            maxlength="1"
            class="sb"
            :style="codeerror ? 'border-color:red;' : ' border-color: #f0f0f0;'"
            @keyup="keyup"
            @keypress="keypress"
          />
          <b-input
            id="two"
            ref="two"
            v-model="two"
            type="number"
            maxlength="1"
            class="sb"
            :style="codeerror ? 'border-color:red;' : ' border-color: #f0f0f0;'"
            @keyup="keyup"
            @keypress="keypress"
          />
          <b-input
            id="three"
            ref="three"
            v-model="three"
            type="number"
            maxlength="1"
            class="sb"
            :style="codeerror ? 'border-color:red;' : ' border-color: #f0f0f0;'"
            @keyup="keyup"
            @keypress="keypress"
          />
          <b-input
            id="four"
            ref="four"
            v-model="four"
            type="number"
            maxlength="1"
            class="sb"
            :style="codeerror ? 'border-color:red;' : ' border-color: #f0f0f0;'"
            @keyup="keyup"
            @keypress="keypress"
          />
          <b-input
            id="five"
            ref="five"
            v-model="five"
            type="number"
            maxlength="1"
            class="sb"
            :style="codeerror ? 'border-color:red;' : ' border-color: #f0f0f0;'"
            @keyup="keyup"
            @keypress="keypress"
          />
          <b-input
            id="six"
            ref="six"
            v-model="six"
            type="number"
            maxlength="1"
            class="sb"
            :style="codeerror ? 'border-color:red;' : ' border-color: #f0f0f0;'"
            @keyup="keyup"
            @keypress="keypress"
          />
        </div>
        <b-row class="b">
          <b-col cols="6" class="text-center">
            <span
              v-show="timeleft > 0"
              class="theme-font-light"
              style="font-size:12px;color:#888888;margin-left:20%;"
              >Resend code in 0:{{
                timeleft > 9 ? "" + timeleft : "0" + timeleft
              }}</span
            >
            <a style="vertical-align: top;" @click="sendcode">
              <span
                v-show="timeleft === 0"
                class="theme-font-light text-center"
                style="font-size:12px;margin-left:20%;cursor:pointer;"
                >Send code again</span
              >
            </a>
          </b-col>
          <b-col cols="6" class="text-center">
            <button class="btn btn-primary" type="submit" @click="next()">
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
      // let kc = event.keyCode;
      // const numVal = event.target.value;
      // console.log(parseInt(numVal));
      // console.log(numVal);
      if (typeof parseInt(event.key) === "number") {
        return true;
      } else {
        event.preventDefault();
      }
      // if (kc != 8 && kc != 46 && (kc < 48 || kc > 57)) {
      //   event.preventDefault();
      // } else return true;
    },
    keyup(event) {
      const kc = event.keyCode;
      let movenext = true;

      // Backspace
      if (kc === 8) {
        movenext = false;
      }
      // All digits
      if (typeof parseInt(event.key) === "number") {
        switch (event.target.id) {
          case "one":
            if (movenext) this.$refs.two.focus();
            break;
          case "two":
            if (movenext) this.$refs.three.focus();
            else this.$refs.one.focus();
            break;
          case "three":
            if (movenext) this.$refs.four.focus();
            else this.$refs.two.focus();
            break;
          case "four":
            if (movenext) this.$refs.five.focus();
            else this.$refs.three.focus();
            break;
          case "five":
            if (movenext) this.$refs.six.focus();
            else this.$refs.four.focus();
            break;
          case "six":
            if (!movenext) this.$refs.five.focus();
            break;
        }
      }

      if (this.codeerror) {
        if (
          this.one === "" ||
          this.two === "" ||
          this.three === "" ||
          this.four === "" ||
          this.five === "" ||
          this.six === ""
        ) {
          this.codeerror = true;
        } else {
          this.codeerror = false;
        }
      }
    },
    next() {
      if (
        this.one === "" ||
        this.two === "" ||
        this.three === "" ||
        this.four === "" ||
        this.five === "" ||
        this.six === ""
      ) {
        this.codeerror = true;
      } else {
        this.codeerror = false;
        const code =
          this.one + this.two + this.three + this.four + this.five + this.six;
        this.one = "";
        this.two = "";
        this.three = "";
        this.four = "";
        this.five = "";
        this.six = "";
        clearInterval(this.timer);
        this.timer = null;
        this.$emit("onnext", code);
        localStorage.setItem("mobileCode", code);
      }
    },
    close() {
      this.one = "";
      this.two = "";
      this.three = "";
      this.four = "";
      this.five = "";
      this.six = "";
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
      this.two = "";
      this.three = "";
      this.four = "";
      this.five = "";
      this.six = "";

      this.$emit("onresend");

      this.timeleft = 59;
      this.timer = setInterval(this.timerfired, 1000);
    }
  }
};
</script>
