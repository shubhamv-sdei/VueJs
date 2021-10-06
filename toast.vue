<template>
  <div class="d-none">
    <!--        variant="primary"-->
    <!--        variant="secondary"-->
    <!--        variant="danger"-->
    <!--        variant="warning"-->
    <!--        variant="success"-->
    <!--        variant="info"-->
    <!--        <b-button variant="info" @click="makeToast('info')" class="mb-2">Info</b-button>-->

    <b-toast id="my-toast" :variant="toast.type" solid b-toaster-top-center>
      <template v-slot:toast-title>
        <div class="d-flex flex-grow-1 align-items-baseline">
          <b-img
            blank
            blank-color="#ff5555"
            class="mr-2"
            width="12"
            height="12"
          ></b-img>
          <strong class="mr-auto">{{ toast.title }}</strong>
        </div>
      </template>
      <h3>{{ toast.body }}</h3>
      <ul v-if="Array.isArray(toast.errors)">
        <li
          v-for="(error, index) in toast.errors"
          :key="index"
          class="text-danger"
        >
          {{ error }}
        </li>
      </ul>
      <p v-else>
        {{ toast.errors }}
      </p>
      <!--      <nuxt-link to="login" class="btn-primary">login</nuxt-link>-->
    </b-toast>
  </div>
</template>

<script>
export default {
  name: "Toast",
  computed: {
    toast() {
      return this.$store.state.toast;
    }
  }
};
</script>
