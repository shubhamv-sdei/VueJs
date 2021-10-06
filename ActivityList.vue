<template>
  <div class="activitiesList">
    <div v-for="(item, index) in dataactivity" :key="index" class="">
      <div
        class="show-button mb-0 d-flex"
        @click="
          contentVisible === index
            ? (contentVisible = false)
            : (contentVisible = index)
        "
      >
        <img class="img" :src="item.user.imageUrl" alt="Avatar" />
        <div>
          <p class="Date">{{ timeAgo(item.createdAt) }}</p>
          <p class="Message">
            you have received a new rating from {{ item.user.firstName }}
            {{ item.user.lastName }}
          </p>
        </div>
      </div>
      <div v-show="contentVisible === index" class="item-details">
        <p class="Date">{{ item.user.email }}</p>
        <p class="Message">{{ item.user.phone }}</p>
        <p class="Message">{{ item.user.rating }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import { TimeAgo } from "@/plugins/mixins/timeAgo";

export default {
  name: "ActivityList",
  mixins: [TimeAgo],
  props: {
    dataactivity: {
      type: Array,
      default() {
        return [];
      }
    }
  },
  data() {
    return {
      contentVisible: false
    };
  },

  methods: {
    showContent() {
      this.show = !this.show;
    }
  }
};
</script>
