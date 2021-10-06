<template>
  <div
    id="gmapContainerId"
    class="gmapContainer"
    @mousedown="startMapMove"
    @mouseup="disableMapMove"
  >
    <div v-if="showLoading" class="api-loading">
      <div class="row">
        <div class="col-10">
          Receiving listings from Nexme
        </div>
        <div class="col-2">
          {{ loadingCount }}
        </div>
      </div>
      <div class="row">
        <div class="col-12">
          <b-progress :value="loadingCount" :max="100"></b-progress>
        </div>
      </div>
    </div>
    <div class="mapLayerOption">
      <div class="layerBtn" @click="showlayerOptions = !showlayerOptions">
        Map
        <div class="arrowIcn"></div>
      </div>
      <div v-if="showlayerOptions" class="mapOptions">
        <div class="mapOp" :class="mapActive" @click="changeLayer('map')">
          Map
        </div>
        <div class="satOp" :class="satActive" @click="changeLayer('sat')">
          Satellite
        </div>
      </div>
    </div>
    <div class="mapZoomButtons">
      <div class="zoomMap" @click="changeMapZoom('in')">
        <img src="@/assets/icons/plus.svg" alt="Zoom in" />
      </div>
      <div class="zoomOutMap" @click="changeMapZoom('out')">
        <img src="@/assets/icons/minus.svg" alt="Zoom out" />
      </div>
    </div>
    <GmapMap
      ref="gmap"
      :center="mapCity.data ? mapCity.data : currentCenter"
      :zoom="currentZoom"
      style="width: 100%;height:100%;"
      :options="{
        zoomControl: !mOptions.showZoom,
        mapTypeControl: true,
        scaleControl: false,
        streetViewControl: false,
        rotateControl: false,
        fullscreenControl: true,
        disableDefaultUi: true,
        styles: styles,
        gestureHandling: 'greedy'
      }"
      @center_changed="centerchanged($event)"
      @zoom_changed="zoomChanged($event)"
      @idle="gMapIdle()"
    >
      <google-map-cluster
        :max-zoom="15"
        :minimum-cluster-size="minCluster"
        :zoom-on-click="true"
        :grid-size="100"
        :styles="clusterStyles"
      >
        <gmap-custom-marker
          v-for="(m, index) in markers"
          :key="index"
          :marker="m.position"
          :class="
            selectedMarker == m.info.mls
              ? 'marker' + m.info.mls + ' selected-marker'
              : 'marker' + m.info.mls
          "
          @click.native="markerClicked(m)"
        >
          <ListingMarker :listing="m.info" />
        </gmap-custom-marker>
      </google-map-cluster>
    </GmapMap>
  </div>
</template>

<script>
import { mapState } from "vuex";
import ListingMarker from "./ListingMarker";

export default {
  name: "Map",
  components: {
    ListingMarker
  },
  props: {
    markers: {
      type: Array,
      default: () => {
        return [];
      }
    },
    mapCity: {
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
      userMapMove: false,
      isdragging: false,
      markerList: [],
      markerClickedVal: 0,
      currentCenter: {},
      showlayerOptions: false,
      currentZoom: 14,
      satActive: false,
      mapActive: false,
      draggingCenter: {},
      selectedMarker: 1,
      minCluster: 150,
      theLatLngs: {},
      showLoading: false,
      loadingCount: 10,
      isSearchFierd: true,
      mlsPerPage: [],
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
  computed: {
    ...mapState(["tempCoords"]),
    geoStatus() {
      return this.$store.getters.getGeoStatus;
    },
    setMapCenter() {
      return this.$store.getters.getGeometry.location;
    },
    mapCenterUpdate() {
      return this.$store.getters.getGeometry.location;
    },
    mapDataUpdate: {
      get() {
        return this.$store.getters.getSearchList;
      },
      set(val) {
        return val;
      }
    },
    reRunBounds() {
      return this.mOptions.showZoom;
    },
    getLoadingStatus() {
      return this.$store.getters.getApiLoadingStatus;
    },
    getSearchStatus() {
      return this.$store.getters.getSearchFierd;
    },
    getMlsList() {
      return this.$store.getters.getFavListingsMls;
    }
  },
  watch: {
    tempCoords() {
      this.currentCenter = this.$store.getters.getGeometry.location;
    },
    getSearchStatus(st) {
      this.isSearchFierd = st;
    },
    mapDataUpdate(data) {
      const $this = this;
      this.markerList = data;

      this.getPageMlsData();

      if (this.isSearchFierd) {
        setTimeout(function() {
          $this.mapFitBounds();
        }, 300);
      }
    },
    reRunBounds() {
      const $this = this;
      setTimeout(function() {
        $this.mapFitBounds();
      }, 100);
    },
    getLoadingStatus(s) {
      this.showLoading = s;
    },
    getMlsList(dt) {
      this.mlsPerPage = dt;
      const $this = this;
      if (this.isSearchFierd) {
        setTimeout(function() {
          $this.mapFitBounds();
        }, 300);
      }
    }
  },
  mounted() {
    this.showLoading = true;
    this.mapActive = "active";
    this.showProgress();
    const $this = this;

    if (this.isSearchFierd) {
      const allList = this.getMlsList;
      if (allList.length !== 0) {
        this.mapDataUpdate = allList;
        setTimeout(function() {
          $this.mapFitBounds();
        }, 300);
      }
    }
    const locationDt = localStorage.getItem("favGeometry");
    if (locationDt !== null) {
      const lcData = JSON.parse(locationDt);
      this.currentCenter = lcData.location;
    } else {
      navigator.geolocation.getCurrentPosition((position) => {
        const center = {
          lat: position.coords.latitude,
          lng: position.coords.longitude
        };

        if (
          $this.$store.getters.getGeometry.lat === undefined ||
          $this.$store.getters.getGeometry.lng === undefined
        ) {
          $this.currentCenter = center;
        } else {
          $this.currentCenter = $this.$store.getters.getGeometry;
        }
      });
    }

    window.addEventListener("load", (params) => {
      if (!this.isSearchFierd) {
        $this.mapFitBounds();
      }
    });

    const checkData = new Promise((resolve, reject) => {
      const allList = $this.getMlsList;
      resolve(allList);
    });

    checkData.then((successMessage) => {
      $this.getPageMlsData();
    });

    setTimeout(function() {
      $this.mapFitBounds();
    }, 500);
  },
  methods: {
    changeMapZoom(type) {
      if (type === "in") {
        document
          .querySelector(".gmnoprint button.gm-control-active[title*='in']")
          .click();
      } else {
        document
          .querySelector(".gmnoprint button.gm-control-active[title*='out']")
          .click();
      }
    },
    changeLayer(type) {
      this.showlayerOptions = false;
      if (type === "map") {
        this.mapActive = "active";
        this.satActive = "";
        document
          .querySelector(".gmnoprint .gm-style-mtc div[title*='street']")
          .click();
      } else {
        this.mapActive = "";
        this.satActive = "active";
        document
          .querySelector(".gmnoprint .gm-style-mtc div[title*='satellite']")
          .click();
      }
    },
    showEvent($event) {
      alert($event);
    },
    startMapMove() {
      this.userMapMove = true;
      this.$store.commit("setTopSearchSt", true);
    },
    disableMapMove() {
      const $this = this;
      setTimeout(function() {
        $this.userMapMove = false;
      }, 300);
    },
    mapFitBounds() {
      if (this.markers.length > 300) {
        this.minCluster = 60;
      } else {
        this.minCluster = 100;
      }
      const mainPoint = localStorage.getItem("favGeometry");
      const mpData = mainPoint !== null ? JSON.parse(mainPoint) : null;
      let ctCenter = {};
      if (mpData) {
        ctCenter = {
          lat: mpData.location.lat,
          lng: mpData.location.lng
        };
      } else {
        ctCenter = {
          lat: this.draggingCenter.lat,
          lng: this.draggingCenter.lng
        };
      }

      const bounds = new window.google.maps.LatLngBounds(ctCenter);
      if (this.markers.length !== 0) {
        if (bounds !== null) {
          for (const m of this.markers) {
            bounds.extend(m.position);
          }
          this.$refs.gmap.fitBounds(bounds);
          this.$store.commit("setApiLoadingStatus", false);
          this.loadingCount = 10;
          this.showLoading = false;
          this.isSearchFierd = false;
          this.$store.commit("setSearchFierd", false);
        }
      }
    },
    zoomChanged(zoom) {
      this.currentZoom = zoom;
    },
    gMapIdle() {
      const touchEvnt = document.getElementById("gmapContainerId");
      touchEvnt.addEventListener("touchstart", this.startMapMove, false);
      touchEvnt.addEventListener("touchend", this.disableMapMove, false);
    },
    centerchanged(obj) {
      const location = JSON.stringify(obj);
      this.draggingCenter = JSON.parse(location);
    },
    markerClicked(marker) {
      this.selectedMarker = marker.info.mls;
      this.$store.commit("setNavigateFromMap", 2);
      this.$store.commit("setPageLoadedSt", 2);

      const thisList = {
        tm: new Date().getTime(),
        mls: marker.info.mls,
        state: marker.info.state
      };
      thisList.tm = new Date().getTime();
      if (this.markerClickedVal === 0) {
        this.$store.commit("setSelectedListing", thisList);
        this.markerClickedVal = thisList.mls;
      } else {
        this.markerClickedVal = 0;
        this.$store.commit("setSelectedListing", thisList);
      }

      this.getPageMlsData(marker.info.mls);
    },
    showProgress() {
      const $this = this;
      setInterval(() => {
        if ($this.loadingCount < 91) {
          $this.loadingCount = $this.loadingCount + 10;
        }
      }, 1000);
    },
    getPageMlsData(mls) {
      const data = this.$store.getters.getFavListingsMls;

      if (mls !== undefined) {
        for (let t = 0; t < data.length; t++) {
          if (data[t] !== undefined) {
            if (data[t].includes(mls)) {
              this.$store.commit("setSelectedPage", t + 1);
            }
          }
        }
      }
    }
  }
};
</script>
