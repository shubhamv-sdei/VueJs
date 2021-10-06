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
    <div class="mapNearbyIcon" role="button" @click="getNearByListing()">
      <div class="icn">
        <img src="@/assets/icons/nearby.svg" alt="Nearby" />
      </div>
      <div class="txt">
        Nearby
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
      <!-- <gmap-circle
          :center="{ lat: currentCenter.lat, lng: currentCenter.lng }"
          :radius="currentZoom * 100"
          >
        </gmap-circle> -->
      <gmap-polyline ref="polygon" :path="theLatLngs" :editable="false">
      </gmap-polyline>
      <!-- <google-map-cluster
        :max-zoom="15"
        :minimum-cluster-size="minCluster"
        :zoom-on-click="true"
        :grid-size="100"
        :styles="clusterStyles"
      > -->
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
      <!-- </google-map-cluster> -->
    </GmapMap>
  </div>
</template>

<script>
import Axios from "axios";
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
      theLatLngs: [],
      showLoading: false,
      loadingCount: 10,
      isSearchFierd: true,
      mlsPerPage: [],
      styles: [
        // { elementType: "geometry", stylers: [{ color: "#f6f6f6" }] },
        { elementType: "labels.text.fill", stylers: [{ color: "#8a98ae" }] },
        // { elementType: "labels.text.stroke", stylers: [{ color: "#8a98ae" }] },
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
      ],
      gMapZoomArr: [
        15000,
        14500,
        14000,
        13500,
        13000,
        12500,
        12000,
        11500,
        11000,
        10000,
        9000,
        8000,
        7000,
        6000,
        5500,
        4900,
        4000,
        3000,
        2500,
        2100
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
      return this.$store.getters.getListingsMls;
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
      // const mlsList = [];
      // for (let d = 0; d <= data.length; d++) {
      //   if (data[d].mls) {
      //     mlsList.push(data[d].mls);
      //   }
      // }

      // let i;
      // let j;
      // let pageData;
      // const chunk = 20;
      // for (i = 0, j = mlsList.length; i < j; i += chunk) {
      //   pageData = mlsList.slice(i, i + chunk);
      //   this.mlsPerPage.push(pageData);
      // }

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
    const locationDt = localStorage.getItem("geometry");
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

    // setTimeout(function() {
    //   This.mapFitBounds();
    // }, 200);
  },
  // updated() {

  // },
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
    getNearByListing() {
      const $this = this;

      navigator.geolocation.getCurrentPosition((position) => {
        const center = {
          lat: position.coords.latitude,
          lng: position.coords.longitude
        };

        const mapDetails = {
          location: center,
          zoom: $this.mpZoomCal($this.currentZoom)
        };

        const lcn = {
          location: center
        };

        localStorage.setItem("geometry", JSON.stringify(lcn));
        $this.$emit("nearby-clicked", mapDetails);
        $this.$store.commit("setGeometry", center);
      });
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
      // const $this = this;
      if (this.markers.length > 300) {
        this.minCluster = 60;
      } else {
        this.minCluster = 100;
      }
      const mainPoint = localStorage.getItem("geometry");
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
      const location = this.draggingCenter;
      // this.draggingCenter = JSON.parse(location);
      const mapDetails = {
        location,
        zoom: this.mpZoomCal(zoom)
      };
      if (this.isSearchFierd) {
        this.$emit("map-moved", this.draggingCenter, zoom);
        this.$store.commit("setMapDetails", mapDetails);
      }
      this.isSearchFierd = false;
      this.$store.commit("setSearchFierd", false);
      this.$store.commit("setMapInfo", mapDetails);
    },
    mpZoomCal(lvl) {
      const data = this.gMapZoomArr;
      return data[lvl];
    },
    gMapIdle() {
      const touchEvnt = document.getElementById("gmapContainerId");
      touchEvnt.addEventListener("touchstart", this.startMapMove, false);
      touchEvnt.addEventListener("touchend", this.disableMapMove, false);
      const mainPoint = localStorage.getItem("geometry");
      if (mainPoint !== null) {
        const mainObj = JSON.parse(mainPoint);
        mainObj.location.lat = this.draggingCenter.lat || mainObj.location.lat;
        mainObj.location.lng = this.draggingCenter.lng || mainObj.location.lng;
        const mainObjToStr = JSON.stringify(mainObj);
        localStorage.setItem("geometry", mainObjToStr);
      }
      this.isSearchFierd = false;
      this.$store.commit("setSearchFierd", false);
      if (this.draggingCenter) {
        // this.currentCenter = this.draggingCenter;
        // this.draggingCenter = null;

        this.$store.commit("setGeometry", {
          lat: this.draggingCenter.lat,
          lng: this.draggingCenter.lng
        });
        if (this.userMapMove) {
          this.$emit("map-moved", this.draggingCenter, this.currentZoom);
        }
      }
    },
    centerchanged(obj) {
      const location = JSON.stringify(obj);
      this.draggingCenter = JSON.parse(location);
      const mapDetails = {
        location: JSON.parse(location),
        zoom: this.mpZoomCal(this.currentZoom)
      };
      if (this.userMapMove) {
        this.$store.commit("setTopSearchSt", true);
      } else {
        this.$store.commit("setTopSearchSt", false);
      }
      this.$store.commit("setMapDetails", mapDetails);
      this.$store.commit("setMapInfo", mapDetails);
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
    getCityData(theCity) {
      const apiPath = "https://nominatim.openstreetmap.org/search.php";
      const $this = this;
      const params = {
        q: theCity,
        polygon_geojson: 1,
        format: "json"
      };

      Axios.get(apiPath, { params }).then((response) => {
        const geoJSONDataChunk = response.data[0];
        const coordinates = geoJSONDataChunk.geojson.coordinates[0];
        $this.theLatLngs = coordinates[0];
        // geojson data from http://nominatim.openstreetmap.org/ needs
        // to be wrapped, so that the google addGeoJson() call
        // can handle it properly
        const geoConf = {
          type: "FeatureCollection",
          features: [
            { type: "Feature", geometry: geoJSONDataChunk.geojson, id: "city" }
          ]
        };

        console.log(geoConf);

        // this.myCityData = new Gmap.maps.Data();
        // this.myCityData.addGeoJson(geoConf, "city");
        // this.myCityData.setStyle({
        //   fillColor: "green",
        //   fillOpacity: 0.1,
        //   strokeWeight: 1
        // });

        // send data to our Map component
        // eventBus.$emit("sendCityData", this.myCityData);
      });
    },
    showProgress() {
      const $this = this;
      setInterval(() => {
        if ($this.loadingCount < 91) {
          $this.loadingCount = $this.loadingCount + 10;
        }
      }, 1000);

      setTimeout(function(){
        const objBound = JSON.parse(localStorage.getItem("boundary")).features[0].geometry.coordinates[0];
        const PushArr = [];
        console.log(objBound);
        objBound.forEach(function(value){
          console.log(value);
          PushArr.push({'lat':value[0],'lng':value[1]});
        })
        // $this.theLatLngs = PushArr;
        console.log(PushArr);

        const geoJSONDataChunk = JSON.parse(localStorage.getItem("boundary")).features[0];
        // const coordinates = geoJSONDataChunk.geojson.coordinates[0];
        $this.theLatLngs = PushArr;
        // geojson data from http://nominatim.openstreetmap.org/ needs
        // to be wrapped, so that the google addGeoJson() call
        // can handle it properly
        const geoConf = {
          type: "FeatureCollection",
          features: [
            { type: "Feature", geometry: geoJSONDataChunk.geometry, id: "city" }
          ]
        };

        console.log(geoConf);
        $this.myCityData = new window.google.maps.Data();
        $this.myCityData.addGeoJson(geoConf, "city");
        $this.myCityData.setStyle({
          fillColor: "green",
          fillOpacity: 0.1,
          strokeWeight: 1
        });

        $this.googleGeometryMultiPoly = new window.google.maps.Polygon({
          paths: $this.theLatLngs
        })

        // send data to our Map component
        // eventBus.$emit("sendCityData", this.myCityData);
        $this.$store.commit("setSearchFierd", false);
      },1000);
      
    },
    getPageMlsData(mls) {
      const data = this.$store.getters.getListingsMls;
      // const mlsList = [];
      // for (let d = 0; d <= data.length; d++) {
      //   if (data[d] !== undefined) {
      //     mlsList.push(data[d].mls);
      //   }
      // }

      // let i;
      // let j;
      // let pageData;
      // const tempList = [];
      // const chunk = 20;
      // for (i = 0, j = mlsList.length; i < j; i += chunk) {
      //   pageData = mlsList.slice(i, i + chunk);
      //   tempList.push(pageData);
      // }
      // this.mlsPerPage = tempList;

      // const mlsListData = this.mlsPerPage;
      // const selectedMls = this.selectedMarker || 1;
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
