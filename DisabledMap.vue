<template>
  <b-row>
    <GmapMap
      v-if="currentCenter"
      :center="currentCenter"
      :zoom="currentZoom"
      style="width: 100%;height:73vh;"
      :options="{
        disableDefaultUi: true,
        zoomControl: false,
        mapTypeControl: false,
        scaleControl: false,
        streetViewControl: false,
        rotateControl: false,
        fullscreenControl: false,
        panControl: false,
        styles: styles
      }"
    >
      <gmap-custom-marker v-if="listing" :marker="listing.position">
        <!-- <ListingMarker :listing="listing.info" /> -->
        <img src="@/assets/icons/f-button.svg" />
      </gmap-custom-marker>
    </GmapMap>
  </b-row>
</template>

<script>
export default {
  name: "DisabledMap",
  props: {
    center: {
      type: Object,
      default: () => {
        return {};
      }
    },
    listing: {
      type: Object,
      default: () => {
        return {};
      }
    }
  },
  data() {
    return {
      currentZoom: 17,
      styles: [
        { elementType: "geometry", stylers: [{ color: "#f6f6f6" }] },
        { elementType: "labels.text.fill", stylers: [{ color: "#8a98ae" }] },
        {
          featureType: "water",
          elementType: "geometry.fill",
          stylers: [{ color: "#e2f0fa" }]
        },
        {
          featureType: "poi.park",
          elementType: "geometry",
          stylers: [{ color: "#eaf5e6" }]
        },
        {
          featureType: "poi.park",
          elementType: "labels.text.fill",
          stylers: [{ color: "#6b9a76" }]
        }
      ]
    };
  },
  computed: {
    currentCenter() {
      return this.$props.center;
    }
  }
};
</script>

<style lang="scss" scoped></style>
