<template>
  <div class="agent-tutorial">
    <b-modal
      id="agentTutModal"
      ref="agentTutModal"
      v-model="ctData"
      v-b-modal.modal-xl
      hide-footer
      centered
      title=""
      modal-class="agent-tut-modal"
    >
      <div class="row">
        <div class="col-md-12 text-center slide-one">
          <hooper
            v-if="showHooper"
            :settings="hooperSettings"
            @slide="updateCarousel"
          >
            <slide>
              <img
                class="img-fluid"
                src="@/assets/images/tutorial/slide-1.png"
                alt=""
              />
              <h1 class="font-30 fw-600">Welcome to the Nexme team!</h1>
            </slide>
            <slide>
              <img
                class="img-fluid"
                src="@/assets/images/tutorial/slide-2@2x.png"
                alt=""
              />
              <h1 class="font-30 fw-600">Availability</h1>
              <p class="font-14">
                You will only receive requests to tour when you are available in
                the app.
              </p>
            </slide>
            <slide>
              <img
                class="img-fluid"
                src="@/assets/images/tutorial/slide-3.png"
                alt=""
              />
              <h1 class="font-30 fw-600">Requests</h1>
              <p class="font-14">
                Requests can be for an instant tour, a scheduled tour, or an
                offer on a home. Accept or decline any requests at your
                discretion.
              </p>
            </slide>
            <slide>
              <img
                class="img-fluid"
                src="@/assets/images/tutorial/slide-4.svg"
                alt=""
              />
              <h1 class="font-30 fw-600">More info</h1>
              <p class="font-14">
                If you have questions about payment, commission, or any other
                app functions, visit our faq at
                <a
                  href="https://nexme.com/agent/become-an-agent#becomeAnAgentFaq"
                  target="_blank"
                  class="fw-600"
                  >visit our faq</a
                >
              </p>
            </slide>
            <hooper-pagination
              v-if="!lastSlide"
              slot="hooper-addons"
            ></hooper-pagination>
            <hooper-navigation
              v-if="!lastSlide"
              slot="hooper-addons"
            ></hooper-navigation>
          </hooper>
          <div v-else class="loading-universal">
            <div class="loader"></div>
          </div>
          <div v-if="lastSlide" class="doneBtn">
            <button class="btn btn-primary minsize" @click="doneTutorial()">
              Done
            </button>
          </div>
        </div>
      </div>
    </b-modal>
  </div>
</template>

<script>
import {
  Hooper,
  Slide,
  Pagination as HooperPagination,
  Navigation as HooperNavigation
} from "hooper";
import "hooper/dist/hooper.css";
export default {
  name: "AgentTutorial",
  components: {
    Hooper,
    Slide,
    HooperPagination,
    HooperNavigation
  },
  props: {
    ctData: {
      type: Boolean
    }
  },
  data() {
    return {
      showHooper: false,
      lastSlide: false,
      slideNum: 0,
      hooperSettings: {
        itemsToShow: 1,
        itemsToSlide: 1,
        infiniteScroll: false
      }
    };
  },
  mounted() {
    const $this = this;
    setTimeout(() => {
      $this.showHooper = true;
    }, 500);

    this.$root.$on("bv::modal::hide", (bvEvent, modalId) => {
      this.doneTutorial();
    });
  },
  methods: {
    updateCarousel(payload) {
      this.slideNum = payload.currentSlide;
      if (payload.currentSlide === 3) {
        this.lastSlide = true;
      }
    },
    doneTutorial() {
      // this.$refs.agentTutModal.hide();
      this.$emit("closeTut", true);
      localStorage.setItem("agentTutDone", "yes");
    }
  }
};
</script>
