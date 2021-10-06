<template>
  <b-modal
    ref="verifyEmailModal"
    centered
    hide-footer
    title="Verify your email address"
  >
    <div class="verify-email-box mt-2">
      <div class="row">
        <div class="col-md-12 pr-0 pl-0">
          <!-- <h5 class="font-16 fw-600 color-dark">Verify your email address</h5> -->
          <p class="font-16 color-base">
            You need a verified email address to have access to our home lenders
            and inspectors.
          </p>
        </div>
      </div>
      <div class="row">
        <div class="col-md-12 text-right">
          <button
            v-if="!sendEmailSt"
            class="btn btn-primary block fw-regular mt-4"
            @click="resendVerification"
          >
            Resend verification email
          </button>
          <div v-else class="font-13 mt-4 mb-4 fw-regular color-dark-green">
            <div class="checkmark"></div>
            Check your email for a verification link!
          </div>
        </div>
      </div>
    </div>
  </b-modal>
</template>

<script>
export default {
  name: "VerifyEmail",
  props: {
    sendEmailSt: Boolean,
    startEmailMd: Boolean
  },
  data() {
    return {
      checklogin: false,
      emailSentStatus: false
    };
  },
  mounted() {
    this.$on("show-verify-email", this.showModal());
    const $this = this;
    this.$root.$on("bv::modal::hide", (e) => {
      $this.hideModal();
    });
  },
  methods: {
    resendVerification() {
      const obj = {
        type: "POST",
        url: "Account/SendVerifyEmail"
      };
      this.$store.dispatch("sendUserRequests", obj);
      // this.emailSentStatus = true;
    },
    showModal() {
      this.$refs.verifyEmailModal.show();
    },
    hideModal() {
      const $this = this;
      setTimeout(() => {
        $this.$emit("showVerifyEmail", false);
      }, 500);
    }
  }
};
</script>
