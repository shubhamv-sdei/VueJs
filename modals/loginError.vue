<template>
  <b-modal ref="$loginModal" centered hide-footer>
    <template v-slot:modal-title>
      <div class="col-md-12">
        You need to be signed in to do that!
      </div>
    </template>
    <div class="d-block row">
      <div class="col-md-12">
        <p class="font-14">
          Create an account or sign in to continue
        </p>
      </div>
    </div>
    <div class="row">
      <div class="col-12">
        <button class="mt-3 btn btn-primary" @click="goToLogin()">
          Sign in / Create an account
        </button>
      </div>
    </div>
  </b-modal>
</template>

<script>
export default {
  name: "LoginError",
  data() {
    return {
      done: ""
    };
  },
  mounted() {
    this.$refs.$loginModal.show();
  },
  methods: {
    goToLogin() {
      this.$router.push({ name: "login" });
    }
  }
};
</script>
