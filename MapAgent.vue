<template>
  <div class="smallMapContainer">
    <GmapMap
      ref="gmap"
      :center="mapCenter"
      :zoom="15"
      style="width: 100%;height:100%;"
      :options="{
        zoomControl: false,
        mapTypeControl: false,
        scaleControl: false,
        scrollwheel: false,
        streetViewControl: false,
        draggable: false,
        rotateControl: false,
        fullscreenControl: false,
        disableDefaultUi: true,
        styles: styles,
        gestureHandling: 'greedy'
      }"
    >
      <gmap-custom-marker :marker="mapCenter">
        <div class="mapMarker">
          <img src="@/assets/icons/home-white.svg" />>
        </div>
      </gmap-custom-marker>
      <gmap-custom-marker
        v-for="(m, index) in agents"
        :key="'key' + index"
        :marker="m"
      >
        <div class="agent-map-marker">
          <img src="@/assets/icons/noun_person_737678.svg" />
        </div>
      </gmap-custom-marker>
    </GmapMap>
  </div>
</template>

<script>
import Axios from "axios";

export default {
  name: "MapAgents",
  props: {
    mapCenter: {
      type: Object,
      default: () => {
        return {};
      }
    },
    mOptions: {
      type: Object,
      default: () => {
        return {};
      }
    }
  },
  data() {
    return {
      isdragging: false,
      markerList: [],
      markerClickedVal: 0,
      currentCenter: {},
      currentZoom: 14,
      draggingCenter: {},
      selectedMarker: "",
      minCluster: 40,
      agents: [],
      styles: [
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
      ],
      clusterStyles: [
        {
          textColor: "#fff",
          url: "./assets/empty-cluster.png",
          height: 35,
          width: 35
        }
      ]
    };
  },
  mounted() {
    const $this = this;
    this.getAgentList();
    setTimeout(function() {
      $this.$store.commit("setActiveAgentCount", $this.agents.length);
      $this.mapFitBounds();
    }, 1000);
  },
  methods: {
    getAgentList() {
      const $this = this;
      const Url = this.$apiPath("main", "Agent/GetSurroundingAgents");
      const reqData = {
        searchRadius: 10,
        center: {
          latitude: this.mapCenter.lat,
          longitude: this.mapCenter.lng
        }
      };
      Axios({
        method: "POST",
        url: Url,
        data: reqData,
        headers: {
          "Content-Type": "application/json",
          "X-Client-Token": $this.$store.state.X_CLIENT_TOKEN
        }
      }).then(
        (result) => {
          if (
            result.data.statusCode === "Success" &&
            result.data !== undefined &&
            result.data.data !== undefined
          ) {
            const agentList = result.data.data;
            $this.markerList = agentList.agents;
            $this.agents = agentList.agents;
            $this.$store.commit("setActiveAgentCount", agentList.agents.length);
            $this.$store.commit("setAgentTimeDistance", agentList.time);
          }
        },
        (error) => {
          throw new Error(error);
        }
      );
    },
    mapFitBounds() {
      const bounds = new window.google.maps.LatLngBounds({
        lat: this.mapCenter.lat,
        lng: this.mapCenter.lng
      });
      if (this.agents.length !== 0) {
        if (bounds !== null) {
          for (const m of this.agents) {
            bounds.extend({ lat: m.latitude, lng: m.longitude });
          }
          this.$refs.gmap.fitBounds(bounds);
        }
      }
    }
  }
};
</script>
