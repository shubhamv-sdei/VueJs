<template>
  <div
    v-if="
      searchFunction(
        mortgageProvider.fullName !== undefined
          ? mortgageProvider.fullName
          : mortgageProvider.name
      )
    "
    class="col-12 provider shadow-sm"
  >
    <div class="d-flex align-items-stretch">
      <div class="provider-img-wrp">
        <img
          :alt="
            mortgageProvider.fullName !== undefined
              ? mortgageProvider.fullName
              : mortgageProvider.name
          "
          class="img-fluid provider-image"
          :src="setImage(mortgageProvider.logoUrl)"
        />
      </div>
      <div class="details">
        <div>
          <h4 class="provider-title">
            {{
              mortgageProvider.fullName !== undefined
                ? mortgageProvider.fullName
                : mortgageProvider.name
            }}
            <div class="provider-star">
              <img
                v-if="mortgageProvider.stars !== undefined"
                src="@/assets/icons/star.svg"
                alt=""
              />
              <img v-else src="@/assets/icons/star-gray.svg" alt="" />
              <span class="fw-light"
                >{{
                  mortgageProvider.stars !== undefined
                    ? mortgageProvider.stars + " stars"
                    : "Not rated yet"
                }}
              </span>
            </div>
          </h4>
          <div class="description">
            {{
              mortgageProvider.description !== undefined
                ? mortgageProvider.description
                : "No description"
            }}
          </div>
          <a href="#" class="btn btn-primary">Get Started</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "MortgageProviders",
  props: {
    searchText: {},
    mortgageProvider: {
      type: Object,
      default: () => {
        return {};
      }
    }
  },

  methods: {
    setImage(imageUrl) {
      if (imageUrl !== undefined && imageUrl !== "" && imageUrl !== null)
        return imageUrl;
      return "/assets/images/home-image3.png";
    },
    searchFunction(searchText) {
      return searchText.toLowerCase().includes(this.searchText.toLowerCase());
    }
  }
};
</script>

<style scoped>
@media (min-width: 1024px) {
.mortgage-providers .d-flex .details {
width: calc(100% - 200px) !important;
padding: 10px 15px !important;
}
.mortgage-providers .d-flex .provider-img-wrp {
width: 180px !important;
}
}
</style>
