<template>
  <b-row class="availability">
    <b-col cols="12" sm="12" md="12" lg="5" xl="5" class="pl-0">
      <b-row class="text theme-font-semi-bold fix-space">
        <span
          >Where is <span class="secondarycolor">Nexme</span> available?</span
        >
      </b-row>
      <b-row class="subtitletext theme-font-regular fw-400"
        >Nexme is available in Washington state.
        <p class="color-grey-a fw-400">
          California, Massachusetts, & Rhode Island coming soon!
        </p></b-row
      >
    </b-col>
    <b-col
      lg="7"
      sm="12"
      md="12"
      class="d-none d-sm-block d-md-block d-lg-block d-xl-block"
    >
      <b-row align-h="start" no-gutters>
        <b-col cols="3" class="text-center">
          <img src="@/assets/icons/Washington.svg" />
        </b-col>
        <b-col cols="3" class="text-center">
          <img src="@/assets/icons/california.svg" />
        </b-col>
        <b-col cols="3" class="text-center">
          <img src="@/assets/icons/massachusetts.svg" />
        </b-col>
        <b-col cols="3" class="text-center">
          <img src="@/assets/icons/rhode-island.svg" />
        </b-col>
      </b-row>
    </b-col>
    <b-col cols="12" class="d-sm-none d-md-none d-lg-none d-xl-none">
      <b-row align-h="center" no-gutters>
        <b-col cols="3" class="text-center">
          <img src="@/assets/icons/Washington.svg" width="53" height="34" />
        </b-col>
        <b-col cols="3" class="text-center">
          <img src="@/assets/icons/california.svg" width="38" height="45" />
        </b-col>
        <b-col cols="3" class="text-center">
          <img src="@/assets/icons/massachusetts.svg" width="72" height="44" />
        </b-col>
        <b-col cols="3" class="text-center">
          <img src="@/assets/icons/rhode-island.svg" width="34" height="53" />
        </b-col>
      </b-row>
    </b-col>
  </b-row>
</template>
<script>
export default {
  name: "Availability"
};
</script>
