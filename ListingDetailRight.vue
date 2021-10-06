<template>
  <b-row class="mainbox">
    <div class="row action-icons">
      <div class="col-12 listing-logo desktop-view">
        <img src="@/assets/icons/logo_green.svg" width="176" height="27" />
      </div>

      <div class="pl-0 d-flex justify-content-between top-crcl-buttons">
        <div class="circleBtn share_btn" @click="$bvModal.show('shareModal')">
          <img src="@/assets/icons/share_icon.svg" class="inner" />
        </div>

        <div
          v-if="!favorited"
          class="circleBtn heart_btn"
          @click="like(listing.mls)"
        >
          <img src="@/assets/icons/heart.svg" class="inner" />
        </div>

        <div v-else class="circleBtn heart_btn" @click="disLike(listing.mls)">
          <img src="@/assets/icons/heart-red.svg" class="inner" />
        </div>

        <b-dropdown
          size="lg"
          variant="link"
          class="shareButton additionalLint"
          no-caret
        >
          <template v-slot:button-content>
            <ImageButton
              class="circleBtn"
              :w="21"
              :h="3"
              source="ellipsis_icon.svg"
            />
          </template>
          <b-dropdown-item v-b-modal.shareModal href="#">Share</b-dropdown-item>
          <b-dropdown-item
            v-if="!favorited"
            href="#"
            @click.prevent="like(listing.mls)"
            >Favorite</b-dropdown-item
          >
          <b-dropdown-item v-else href="#" @click.prevent="disLike(listing.mls)"
            >Unfavorite</b-dropdown-item
          >
          <b-dropdown-item href="#" @click.prevent="tournowclicked()"
            >Tour now</b-dropdown-item
          >
          <b-dropdown-item href="#" @click.prevent="makeanoffer()"
            >Make an offer</b-dropdown-item
          >
        </b-dropdown>
      </div>
    </div>
    <b-row
      v-if="listing.hasOpenHouse || (listing.status && listing.status !== 'A')"
      class="status-wrapper prop-status-top"
    >
      <div class="col-md-12 pl-0">
        <div
          v-if="listing.hasOpenHouse"
          class="openHouseIcon"
          :class="hasLiveStream() ? 'liveStreamPr' : ''"
        >
          <!-- listing.hasOpenHouse -->
          <img
            v-if="!hasLiveStream()"
            class="sLp"
            src="@/assets/icons/open_sign_details.svg"
          />
          <div class="time-view">
            <div
              v-if="hasLiveStream()"
              class="liveText p-click"
              @click.prevent="liveStreamJoin(listing.openHouses[0])"
            >
              LIVE STREAM
            </div>
            {{ openHouseTime(listing.openHouses[0]) }}
          </div>
        </div>
        <div
          v-if="listing.status && listing.status !== 'A'"
          class="property-status"
          :class="crStatus.class"
        >
          {{ crStatus.status }}
          {{ crStatus.status === "Sold" ? soldDate(listing.sellingDate) : "" }}
        </div>
      </div>
    </b-row>
    <b-row class="mp-r-5 mp-l-16 desktop-view">
      <div class="col-md-12 pr-0 pl-0">
        <span v-if="listing.address" class="address fw-600">
          {{ listing.address }}
        </span>
        <span v-else class="address fw-600"
          >{{ listing.house }} {{ listing.house }}
          {{ listing.directionalPrefix }} {{ listing.street }}
          {{ listing.streetSuffix }} {{ listing.directionalSuffix }}
          {{ listing.unit }} {{ listing.city }} {{ listing.state }}
          {{ listing.zip }}</span
        >
      </div>
    </b-row>
    <b-row class="mp-r-5 mp-l-16 margin-t-0">
      <div class="col-12 pl-0 pr-0 d-flex align-items-center">
        <div class="pl-0 d-flex align-items-baseline">
          <span class="price theme-font-semi-bold fw-600"
            >${{ numberWithCommas(listing.listingPrice) }}</span
          >
          <span class="font-11 pl-3 mobile-view fw-500"
            >&emsp;
            <span class="fw-600">&nbsp;{{ listing.userViewsCount }}&nbsp;</span>
            viewed
            <span class="fw-600">&nbsp;{{ listing.favoriteCount }}&nbsp;</span>
            favorited</span
          >
        </div>
        <div class="desktop-view pl-3">
          <span class="stats theme-font-light fw-400"
            >{{ listing.bedrooms }} Beds | {{ listing.baths }} Baths |
            {{ numberWithCommas(listing.totalSquareFoot) }} sq. ft.</span
          >
        </div>
      </div>
    </b-row>
    <b-row class="cashback mp-r-5 mp-l-16">
      <div class="col-md-12 pl-0">
        <img src="@/assets/icons/cashback.svg" />
        <span class="theme-font-light fw-600">Cash Back</span>
        <span class="theme-font-light secondarycolor fw-600"
          >${{ numberWithCommas(listing.cashBack.toFixed(2)) }}&nbsp;</span
        >
        <img
          v-b-popover.hover.top="info.cashBack"
          src="@/assets/icons/info.svg"
          title="What is cash back?"
          variant="primary"
        />
      </div>
    </b-row>
    <b-row class="views mp-r-5 mp-l-16 desktop-view">
      <span class="theme-font-regular fw-600 font-12">
        {{ listing.userViewsCount }}&nbsp;
      </span>
      <span class="theme-font-light fw-600 font-12">viewed&nbsp;</span>
      <span class="theme-font-regular fw-600 font-12">
        {{ listing.favoriteCount }}&nbsp;
      </span>
      <span class="theme-font-light fw-600 font-12">favorited</span>
    </b-row>
    <b-row>
      <div class="mt-2"></div>
    </b-row>
    <b-row class="mp-r-5 mp-l-0 tour-acion-buttons">
      <div class="col-12 d-flex">
        <button
          class="btn btn-green btn-block schedule"
          @click="scheduleclicked()"
        >
          <img src="@/assets/icons/Schedule_icon.svg" />
          <div class="font-13">
            Schedule tour
          </div>
        </button>
        <!-- </div> col-sm-5 col-md-5
      <div class="col-10 col-sm-7 col-md-7 pl-0"> -->
        <button
          class="btn btn-primary btn-block tournow"
          @click="tournowclicked()"
        >
          Tour Now
          <div class="font-8 fw-600">
            {{ getAgentCount }}
            {{ getAgentCount === 1 ? "Agent" : "Agents" }} within
            {{ getTimeDistance }} minutes
          </div>
        </button>
      </div>
    </b-row>
    <div v-if="showAppRequest" class="row mp-r-5 mp-l-5">
      <div class="col-md-12 request-app-box">
        <p class="font-16 mt-3">
          Sorry, scheduling a tour and instant tour are only available through
          the app right now! Download the app to take a look at this home with
          Nexme.
        </p>
        <h4 class="font-14">We’ll text you an app download link!</h4>
        <div class="input-group col-md-8 col-sm-12 col-sx-12 pl-0">
          <input
            v-model="appPhone"
            v-format-phone
            type="text"
            class="form-control formAttachedInput"
            placeholder="(123) 456-7890"
            maxlength="14"
          />
          <button
            class="input-group-append formAttachedBtn"
            :disabled="!appPhone"
            @click="submitAppRequest()"
          >
            {{ appRequestSent ? "" : "Send" }}
            <span v-if="appRequestSent" class="check-mark"></span>
          </button>
        </div>
        <p
          class="font-12 mt-1"
          v-html="
            appRequestSent
              ? '<b>Text message sent!</b>'
              : 'Standard text messaging rates apply'
          "
        ></p>
      </div>
    </div>
    <b-row class="desktop-view">
      <b-col md="12" class="p-0 mt-1">
        <hr />
      </b-col>
    </b-row>
    <div class="row mobile-view">
      <div class="col-3">
        <img src="@/assets/icons/Bedroom_icon.svg" alt="Bedrooms" />
        <p class="font-10 fw-500 text-wrap mt-2">
          {{ listing.bedrooms }} Bedrooms
        </p>
      </div>
      <div class="col-3">
        <img src="@/assets/icons/Shower_icon.svg" alt="Bedrooms" />
        <p class="font-10 fw-500 text-wrap mt-2">
          {{ listing.baths }} Bathrooms
        </p>
      </div>
      <div class="col-3">
        <img src="@/assets/icons/Sqft_icon.svg" alt="Bedrooms" />
        <p class="font-10 fw-500 text-wrap mt-2">
          {{ numberWithCommas(listing.totalSquareFoot) }} sq. ft.
        </p>
      </div>
    </div>
    <b-row class="mp-r-5 mp-l-16 margin-t-0 desktop-view">
      <b-col md="12" class="p-0">
        <span class="theme-font-semi-bold headers">Overview</span>
      </b-col>
    </b-row>
    <b-row class="mp-r-16 mp-l-16">
      <b-col md="12" class="p-0">
        <p class="description font-16 fw-400">
          {{ listingDescription }}{{ readMoreExpend ? "." : "..."
          }}<span
            v-if="showReadMore"
            class="read-more color-green"
            @click="showReadMoreBtn"
            >{{ showMoreText }}
          </span>
        </p>
        <hr class="desktop-view" />
      </b-col>
    </b-row>
    <div class="row">
      <div class="col-md-12">
        <MapAgent
          :map-center="{ lat: listing.latitude, lng: listing.longitude }"
        />
      </div>
      <div class="col-md-12 mp-l-12 pr-0 pl-0">
        <span v-if="listing.address" class="address fw-500">
          {{ listing.address }}
        </span>
        <span v-else class="address fw-500"
          >{{ listing.house }} {{ listing.house }}
          {{ listing.directionalPrefix }} {{ listing.street }}
          {{ listing.streetSuffix }} {{ listing.directionalSuffix }}
          {{ listing.unit }} {{ listing.city }} {{ listing.state }}
          {{ listing.zip }}</span
        >
      </div>
      <div class="col-md-12 font-12 color-green">
        {{ getAgentCount }}
        {{ getAgentCount === 1 ? "Agent" : "Agents" }} within
        {{ getTimeDistance }} minutes
      </div>
      <div class="col-md-12 pl-0 pr-0">
        <hr />
      </div>
    </div>

    <b-row class="mp-r-5 mp-l-16">
      <div class="col-md-12 pl-0 pr-0">
        <span class="theme-font-semi-bold headers">Key details</span>
      </div>
    </b-row>
    <b-row class="mp-r-5 mp-l-16">
      <span class="theme-font-semi-bold subheaders">Price insights</span>
    </b-row>
    <b-row class="kd theme-font-light mp-r-5 mp-l-16">
      <b-row>
        <b-col class="mp-l-0">List Price</b-col>
        <b-col class="mp-l-0">
          <b>${{ numberWithCommas(listing.listingPrice) }}</b>
        </b-col>
      </b-row>
      <b-row>
        <b-col class="mp-l-0">
          <span>Est. Mo. Payment</span>
        </b-col>
        <b-col class="mp-l-0">
          <b>${{ numberWithCommas(monthlyPayment) }}</b>
          <!-- <b>${{ numberWithCommas(listing.estMortgage.toFixed(2)) }}</b> -->
        </b-col>
      </b-row>
      <b-row v-if="listing.totalSquareFoot !== 0">
        <b-col class="mp-l-0">
          <span>Price/Sq. Ft.</span>
        </b-col>
        <b-col class="mp-l-0">
          <b
            >${{
              numberWithCommas(
                (listing.listingPrice / listing.totalSquareFoot).toFixed(2)
              )
            }}</b
          >
        </b-col>
      </b-row>
    </b-row>
    <b-row class="mp-r-5 mp-l-16">
      <div class="col-md-12 pl-0 pr-0">
        <span class="theme-font-semi-bold headers">Home facts</span>
      </div>
    </b-row>
    <b-row class="kd theme-font-light mp-r-5 mp-l-5">
      <b-row>
        <b-col class="mp-l-12">HOA dues</b-col>
        <b-col class="mp-l-0">
          <b v-if="listing.hoa !== 0" class="fw-500">
            ${{ numberWithCommas(listing.hoa) }}/month
          </b>
          <b v-else class="fw-500">${{ numberWithCommas(listing.hoa) }}</b>
        </b-col>
      </b-row>
      <b-row>
        <b-col class="mp-l-12">
          <span>Property Type</span>
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500" v-html="setPropertyType(listing.type)"></b>
        </b-col>
      </b-row>
      <b-row>
        <b-col class="mp-l-12">
          <span>Style</span>
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500">{{ listing.styleDescription }}</b>
        </b-col>
      </b-row>
      <b-row v-if="listing.unitFloorNumber">
        <b-col class="mp-l-12">
          <span>Floor number</span>
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500">{{ listing.unitFloorNumber }}</b>
        </b-col>
      </b-row>
      <b-row>
        <b-col class="mp-l-12">
          <span>Status</span>
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500">{{ statusValue(listing.status) }}</b>
        </b-col>
      </b-row>
      <b-row v-if="listing.numberOfStoriesInBuilding">
        <b-col class="mp-l-12">
          <span>Stories</span>
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500">{{ listing.numberOfStoriesInBuilding }}</b>
        </b-col>
      </b-row>
      <b-row v-if="listing.viewsDescription">
        <b-col class="mp-l-12">
          <span>View(s)</span>
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500">{{ propStatus(listing.viewsDescription) }}</b>
        </b-col>
      </b-row>
      <b-row>
        <b-col class="mp-l-12">
          <span>Community</span>
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500">{{ listing.community }}</b>
        </b-col>
      </b-row>
      <b-row>
        <b-col class="mp-l-12">
          <span>County</span>
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500">{{ listing.county }}</b>
        </b-col>
      </b-row>
      <b-row>
        <b-col class="mp-l-12">
          <span>MLS#</span>
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500">{{ listing.mls }}</b>
        </b-col>
      </b-row>
      <b-row v-if="listing.yearBuilt !== 0">
        <b-col class="mp-l-12">
          <span>Built</span>
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500">{{ listing.yearBuilt }}</b>
        </b-col>
      </b-row>
      <b-row v-if="listing.lotSquareFoot > 0">
        <b-col class="mp-l-12">
          <span>Lot Size</span>
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500">{{ numberWithCommas(listing.lotSquareFoot) }}</b>
        </b-col>
      </b-row>
      <b-row v-if="listing.totalSquareFoot !== 0">
        <b-col class="mp-l-12">
          <span>Total Sq. Ft.</span>
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500">{{ numberWithCommas(listing.totalSquareFoot) }}</b>
        </b-col>
      </b-row>
      <b-row v-if="listing.squareFootFinished > 0">
        <b-col class="mp-l-12">
          <span>Finished Sq. Ft.</span>
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500">
            {{ numberWithCommas(listing.squareFootFinished) }}
          </b>
        </b-col>
      </b-row>
      <b-row
        v-if="listing.coveredParkingTotal || listing.totalNumberOfParkingSpaces"
      >
        <b-col class="mp-l-12">
          <span>Parking</span>
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500">{{
            listing.coveredParkingTotal || listing.totalNumberOfParkingSpaces
          }}</b>
        </b-col>
      </b-row>
      <b-row v-if="listing.daysOnMarket > 0">
        <b-col class="mp-l-12">
          <span>Days on market</span>
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500">{{ listing.daysOnMarket }}</b>
        </b-col>
      </b-row>
      <b-row v-if="listing.taxYear > 0">
        <b-col class="mp-l-12">
          <span>Tax year</span>
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500">{{ listing.taxYear }}</b>
        </b-col>
      </b-row>
      <b-row>
        <b-col class="mp-l-12">
          <span>Tax amount</span>
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500"
            >${{
              listing.taxAmount
                ? numberWithCommas(listing.taxAmount)
                : propertyTaxAmount
            }}</b
          >
        </b-col>
      </b-row>
      <b-row style="display:none;">
        <b-col class="mp-l-12">
          <span>Buyer’s agent commission</span>
          <img
            v-b-popover.hover.top="agentCommission"
            src="@/assets/icons/info.svg"
            title="Buyer's agent commission"
            variant="primary"
          />
        </b-col>
        <b-col class="mp-l-0">
          <b class="fw-500">{{ listing.agentCommission }}%</b>
        </b-col>
      </b-row>
    </b-row>

    <!-- Agent Details Start From Here -->
    <div v-if="isAgent">
      <b-row class="mp-r-5 mp-l-16">
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Agent details</span>
        </div>
      </b-row>

      <b-row class="kd theme-font-light mp-r-5 mp-l-5">
        <b-row>
          <b-col class="mp-l-12">
            <span>Entry date</span>
          </b-col>
          <b-col class="mp-l-0">
            <b class="fw-500">{{ formatDate(listing.dateListed) }}</b>
          </b-col>
        </b-row>

        <b-row>
          <b-col class="mp-l-12">
            <span>Phone To show</span>
          </b-col>
          <b-col class="mp-l-0">
            <b
              v-if="
                listing.phoneToShow != '0000000000' &&
                  listing.phoneToShow != null
              "
              class="fw-500"
              >{{ formatPhone(listing.phoneToShow) }}</b
            >
            <b v-else>-</b>
          </b-col>
        </b-row>

        <b-row>
          <b-col class="mp-l-12">
            <span>Association phone</span>
          </b-col>
          <b-col class="mp-l-0">
            <b
              v-if="
                listing.associationPhone != '0000000000' &&
                  listing.associationPhone != null
              "
              class="fw-500"
              >{{ formatPhone(listing.associationPhone) }}</b
            >
            <b v-else>-</b>
          </b-col>
        </b-row>

        <b-row>
          <b-col class="mp-l-12">
            <span>Offer review date</span>
          </b-col>
          <b-col class="mp-l-0">
            <b class="fw-500">{{ formatDate(listing.offerReviewDate) }}</b>
          </b-col>
        </b-row>

        <b-row>
          <b-col class="mp-l-12">
            <span>Owner name</span>
          </b-col>
          <b-col class="mp-l-0">
            <b class="fw-500">{{ listing.ownerName }}</b>
          </b-col>
        </b-row>

        <b-row>
          <b-col class="mp-l-12">
            <span>Owner phone</span>
          </b-col>
          <b-col class="mp-l-0">
            <b
              class="fw-500"
              v-if="
                listing.ownerPhone != '0000000000' && listing.ownerPhone != null
              "
              >{{ formatPhone(listing.ownerPhone) }}</b
            >
            <b v-else>-</b>
          </b-col>
        </b-row>

        <b-row>
          <b-col class="mp-l-12">
            <span>Showing info</span>
          </b-col>
          <b-col class="mp-l-0">
            <b class="fw-500">{{ listing.showingInfoDescription }}</b>
          </b-col>
        </b-row>

        <b-row>
          <b-col class="mp-l-12">
            <span>Offers</span>
          </b-col>
          <b-col class="mp-l-0">
            <b class="fw-500">{{ listing.offerReviewType }}</b>
          </b-col>
        </b-row>
      </b-row>
    </div>
    <!-- End of Agent Details -->

    <!-- Here start show full details -->
    <div v-if="showfullDetails">
      <b-row
        v-if="
          listing.bedrooms ||
            listing.bedroomsLower ||
            listing.bedroomsMain ||
            listing.bedroomsUpper ||
            listing.masterBedroomLocation
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Bedroom information</span>
        </div>
      </b-row>
      <b-row
        v-if="
          listing.bedrooms ||
            listing.bedroomsLower ||
            listing.bedroomsMain ||
            listing.bedroomsUpper ||
            listing.masterBedroomLocation
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.bedrooms">
          <b-col class="mp-l-12"
            >Number of bedrooms (total): {{ listing.bedrooms }}</b-col
          >
        </b-row>
        <b-row v-if="listing.bedroomsLower">
          <b-col class="mp-l-12"
            >Number of bedrooms (lower): {{ listing.bedroomsLower }}</b-col
          >
        </b-row>
        <b-row v-if="listing.bedroomsMain">
          <b-col class="mp-l-12"
            >Number of bedrooms (main): {{ listing.bedroomsMain }}</b-col
          >
        </b-row>
        <b-row v-if="listing.bedroomsUpper">
          <b-col class="mp-l-12"
            >Number of bedrooms (upper): {{ listing.bedroomsUpper }}</b-col
          >
        </b-row>
        <b-row v-if="listing.masterBedroomLocation">
          <b-col class="mp-l-12"
            >Master bedroom on {{ listing.masterBedroomLocation }}</b-col
          >
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.baths ||
            listing.fullBaths ||
            listing.halfBaths ||
            listing.threeQuarterBaths ||
            listing.fullBathsMain ||
            listing.halfBathsMain ||
            listing.threeQuarterBathsMain
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Bathroom information</span>
        </div>
      </b-row>
      <b-row
        v-if="
          listing.baths ||
            listing.fullBaths ||
            listing.halfBaths ||
            listing.threeQuarterBaths ||
            listing.fullBathsMain ||
            listing.halfBathsMain ||
            listing.threeQuarterBathsMain
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.baths">
          <b-col class="mp-l-12"
            >Number of baths (total): {{ listing.baths }}</b-col
          >
        </b-row>
        <b-row v-if="listing.fullBaths">
          <b-col class="mp-l-12"
            >Number of baths (full): {{ listing.fullBaths }}</b-col
          >
        </b-row>
        <b-row v-if="listing.halfBaths">
          <b-col class="mp-l-12"
            >Number of main baths (full): {{ listing.halfBaths }}</b-col
          >
        </b-row>
        <b-row v-if="listing.threeQuarterBaths">
          <b-col class="mp-l-12"
            >Number of baths (3/4): {{ listing.threeQuarterBaths }}</b-col
          >
        </b-row>
        <b-row v-if="listing.fullBathsMain">
          <b-col class="mp-l-12"
            >Number of main baths (full): {{ listing.fullBathsMain }}</b-col
          >
        </b-row>
        <b-row v-if="listing.halfBathsMain">
          <b-col class="mp-l-12"
            >Number of main baths (1/2): {{ listing.halfBathsMain }}</b-col
          >
        </b-row>
        <b-row v-if="listing.threeQuarterBathsMain">
          <b-col class="mp-l-12"
            >Number of main baths (3/4):
            {{ listing.threeQuarterBathsMain }}</b-col
          >
        </b-row>
        <b-row v-if="listing.fullBathsLower">
          <b-col class="mp-l-12"
            >Number of lower baths (full): {{ listing.fullBathsLower }}</b-col
          >
        </b-row>
        <b-row v-if="listing.threeQuarterBathsLower">
          <b-col class="mp-l-12"
            >Number of lower baths (3/4):
            {{ listing.threeQuarterBathsLower }}</b-col
          >
        </b-row>
        <b-row v-if="listing.threeQuarterBathsUpper">
          <b-col class="mp-l-12"
            >Number of upper baths (3/4):
            {{ listing.threeQuarterBathsUpper }}</b-col
          >
        </b-row>
        <b-row v-if="listing.halfBathsUpper">
          <b-col class="mp-l-12"
            >Number of upper baths (1/2): {{ listing.halfBathsUpper }}</b-col
          >
        </b-row>
        <b-row v-if="listing.fullBathsGarage">
          <b-col class="mp-l-12"
            >Number of garage baths (full): {{ listing.fullBathsGarage }}</b-col
          >
        </b-row>
        <b-row v-if="listing.halfBathsGarage">
          <b-col class="mp-l-12"
            >Number of garage baths (1/2): {{ listing.halfBathsGarage }}</b-col
          >
        </b-row>
        <b-row v-if="listing.threeQuarterBathsGarage">
          <b-col class="mp-l-12"
            >Number of garage baths (3/4):
            {{ listing.threeQuarterBathsGarage }}</b-col
          >
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.masterBedroomLocation ||
            listing.kitchenWith_eating_space_location ||
            listing.kitchenLocation
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Room information</span>
        </div>
      </b-row>
      <b-row class="kd theme-font-light mp-r-5 mp-l-5">
        <b-row v-if="listing.masterBedroomLocation">
          <b-col class="mp-l-12"
            >Master bedroom on {{ listing.masterBedroomLocation }}</b-col
          >
        </b-row>
        <b-row v-if="listing.kitchenWith_eating_space_location">
          <b-col class="mp-l-12"
            >Kitchen witheating space on
            {{ listing.kitchenWith_eating_space_location }}</b-col
          >
        </b-row>
        <b-row v-if="listing.kitchenLocation">
          <b-col class="mp-l-12"
            >Kitchen without eating space on
            {{ listing.kitchenLocation }}</b-col
          >
        </b-row>
        <b-row v-if="listing.diningRoomLocation">
          <b-col class="mp-l-12"
            >Dining room on {{ listing.diningRoomLocation }}</b-col
          >
        </b-row>
        <b-row v-if="listing.livingRoomLocation">
          <b-col class="mp-l-12"
            >Living room on {{ listing.livingRoomLocation }}</b-col
          >
        </b-row>
        <b-row v-if="listing.familyRoomLocation">
          <b-col class="mp-l-12"
            >Family room on {{ listing.livingRoomLocation }}</b-col
          >
        </b-row>
        <b-row v-if="listing.denLocation">
          <b-col class="mp-l-12">Den/office on {{ listing.denLocation }}</b-col>
        </b-row>
        <b-row v-if="listing.bonusRoomLocation">
          <b-col class="mp-l-12"
            >Bonus room on {{ listing.bonusRoomLocation }}</b-col
          >
        </b-row>
        <b-row v-if="listing.extraFinishedRoomLocation">
          <b-col class="mp-l-12"
            >Extra finished room on
            {{ listing.extraFinishedRoomLocation }}</b-col
          >
        </b-row>
        <b-row v-if="listing.basement">
          <b-col class="mp-l-12">Basement {{ listing.basement }}</b-col>
        </b-row>
        <b-row v-if="listing.utilityRoomLocation">
          <b-col class="mp-l-12"
            >Utility room on {{ listing.utilityRoomLocation }}</b-col
          >
        </b-row>
      </b-row>

      <b-row v-if="listing.floorCovering" class="mp-r-5 mp-l-16">
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Floor information</span>
        </div>
      </b-row>
      <b-row
        v-if="listing.floorCovering"
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-for="item in listing.floorCovering.split(',')" :key="item">
          <b-col class="mp-l-12">{{ item }}</b-col>
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.heatingAndCooling ||
            listing.waterHeaterLocation ||
            listing.heaT_ZONES ||
            listing.coolinG_ZONES ||
            listing.insulationFeatures
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Heating & Cooling</span>
        </div>
      </b-row>
      <b-row
        v-if="
          listing.heatingAndCooling ||
            listing.waterHeaterLocation ||
            listing.heaT_ZONES ||
            listing.coolinG_ZONES ||
            listing.insulationFeatures
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.heaT_ZONES">
          <b-col class="mp-l-12"
            ># of Heating Zones: {{ listing.heaT_ZONES }}</b-col
          >
        </b-row>
        <b-row v-if="listing.coolinG_ZONES">
          <b-col class="mp-l-12"
            ># of Cooling Zones: {{ listing.coolinG_ZONES }}</b-col
          >
        </b-row>
        <b-row v-if="listing.heatingAndCooling">
          <b-col class="mp-l-12"
            >Gas Heating: {{ listing.heatingAndCooling }}</b-col
          >
        </b-row>
        <b-row v-if="listing.heatingAndCooling">
          <b-col class="mp-l-12"
            >Forced Air Conditioning: {{ listing.heatingAndCooling }}</b-col
          >
        </b-row>
        <b-row v-if="listing.heatingAndCooling">
          <b-col class="mp-l-12"
            >Central Air conditioning: {{ listing.heatingAndCooling }}</b-col
          >
        </b-row>
        <b-row v-if="listing.insulationFeatures">
          <b-col class="mp-l-12"
            >Full Insulation: {{ listing.insulationFeatures }}</b-col
          >
        </b-row>

        <!-- <div v-if="listing.waterHeaterLocation">
          <b-row
            v-for="item in listing.waterHeaterLocation.split(',')"
            :key="item"
          >
            <b-col class="mp-l-12">{{ item }}</b-col>
          </b-row>
        </div>
        <div v-if="listing.heatingAndCooling">
          <b-row
            v-for="item in listing.heatingAndCooling.split(',')"
            :key="item"
          >
            <b-col class="mp-l-12">{{ item }}</b-col>
          </b-row>
        </div> -->
      </b-row>

      <b-row
        v-if="
          listing.bedroomsUnit1 ||
            listing.bathroomsUnit1 ||
            listing.squareFeetUnit1 ||
            listing.rentUnit1 ||
            listing.descriptionUnit1
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Unit 1 information</span>
        </div>
      </b-row>
      <b-row
        v-if="
          listing.bedroomsUnit1 ||
            listing.bathroomsUnit1 ||
            listing.squareFeetUnit1 ||
            listing.rentUnit1 ||
            listing.descriptionUnit1
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.bedroomsUnit1">
          <b-col class="mp-l-12"
            >Number of bedrooms: {{ listing.bedroomsUnit1 }}</b-col
          >
        </b-row>
        <b-row v-if="listing.bathroomsUnit1">
          <b-col class="mp-l-12"
            >Number of baths: {{ listing.bathroomsUnit1 }}</b-col
          >
        </b-row>
        <b-row v-if="listing.squareFeetUnit1">
          <b-col class="mp-l-12">Sq. Ft.: {{ listing.squareFeetUnit1 }}</b-col>
        </b-row>
        <b-row v-if="listing.rentUnit1">
          <b-col class="mp-l-12">Monthly rent: {{ listing.rentUnit1 }}</b-col>
        </b-row>
        <b-row v-if="listing.descriptionUnit1">
          <b-col class="mp-l-12"
            >Unit Number: {{ listing.descriptionUnit1 }}</b-col
          >
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.bedroomsUnit2 ||
            listing.bathroomsUnit2 ||
            listing.squareFeetUnit2 ||
            listing.rentUnit2 ||
            listing.descriptionUnit2
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Unit 2 information</span>
        </div>
      </b-row>
      <b-row
        v-if="
          listing.bedroomsUnit2 ||
            listing.bathroomsUnit2 ||
            listing.squareFeetUnit2 ||
            listing.rentUnit2 ||
            listing.descriptionUnit2
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.bedroomsUnit2">
          <b-col class="mp-l-12"
            >Number of bedrooms: {{ listing.bedroomsUnit2 }}</b-col
          >
        </b-row>
        <b-row v-if="listing.bathroomsUnit2">
          <b-col class="mp-l-12"
            >Number of baths: {{ listing.bathroomsUnit2 }}</b-col
          >
        </b-row>
        <b-row v-if="listing.squareFeetUnit2">
          <b-col class="mp-l-12">Sq. Ft.: {{ listing.squareFeetUnit2 }}</b-col>
        </b-row>
        <b-row v-if="listing.rentUnit2">
          <b-col class="mp-l-12">Monthly rent: {{ listing.rentUnit2 }}</b-col>
        </b-row>
        <b-row v-if="listing.descriptionUnit2">
          <b-col class="mp-l-12"
            >Unit Number: {{ listing.descriptionUnit2 }}</b-col
          >
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.bedroomsUnit3 ||
            listing.bathroomsUnit3 ||
            listing.squareFeetUnit3 ||
            listing.rentUnit3 ||
            listing.descriptionUnit3
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Unit 3 information</span>
        </div>
      </b-row>
      <b-row
        v-if="
          listing.bedroomsUnit3 ||
            listing.bathroomsUnit3 ||
            listing.squareFeetUnit3 ||
            listing.rentUnit3 ||
            listing.descriptionUnit3
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.bedroomsUnit3">
          <b-col class="mp-l-12"
            >Number of bedrooms: {{ listing.bedroomsUnit3 }}</b-col
          >
        </b-row>
        <b-row v-if="listing.bathroomsUnit3">
          <b-col class="mp-l-12"
            >Number of baths: {{ listing.bathroomsUnit3 }}</b-col
          >
        </b-row>
        <b-row v-if="listing.squareFeetUnit3">
          <b-col class="mp-l-12">Sq. Ft.: {{ listing.squareFeetUnit3 }}</b-col>
        </b-row>
        <b-row v-if="listing.rentUnit3">
          <b-col class="mp-l-12">Monthly rent: {{ listing.rentUnit3 }}</b-col>
        </b-row>
        <b-row v-if="listing.descriptionUnit3">
          <b-col class="mp-l-12"
            >Unit Number: {{ listing.descriptionUnit3 }}</b-col
          >
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.bedroomsUnit4 ||
            listing.bathroomsUnit4 ||
            listing.squareFeetUnit4 ||
            listing.rentUnit4 ||
            listing.descriptionUnit4
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Unit 4 information</span>
        </div>
      </b-row>
      <b-row
        v-if="
          listing.bedroomsUnit4 ||
            listing.bathroomsUnit4 ||
            listing.squareFeetUnit4 ||
            listing.rentUnit4 ||
            listing.descriptionUnit4
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.bedroomsUnit4">
          <b-col class="mp-l-12"
            >Number of bedrooms: {{ listing.bedroomsUnit4 }}</b-col
          >
        </b-row>
        <b-row v-if="listing.bathroomsUnit4">
          <b-col class="mp-l-12"
            >Number of baths: {{ listing.bathroomsUnit4 }}</b-col
          >
        </b-row>
        <b-row v-if="listing.squareFeetUnit4">
          <b-col class="mp-l-12">Sq. Ft.: {{ listing.squareFeetUnit4 }}</b-col>
        </b-row>
        <b-row v-if="listing.rentUnit4">
          <b-col class="mp-l-12">Monthly rent: {{ listing.rentUnit4 }}</b-col>
        </b-row>
        <b-row v-if="listing.descriptionUnit4">
          <b-col class="mp-l-12"
            >Unit Number: {{ listing.descriptionUnit4 }}</b-col
          >
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.bedroomsUnit5 ||
            listing.bathroomsUnit5 ||
            listing.squareFeetUnit5 ||
            listing.rentUnit5 ||
            listing.descriptionUnit5
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Unit 5 information</span>
        </div>
      </b-row>
      <b-row
        v-if="
          listing.bedroomsUnit5 ||
            listing.bathroomsUnit5 ||
            listing.squareFeetUnit5 ||
            listing.rentUnit5 ||
            listing.descriptionUnit5
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.bedroomsUnit5">
          <b-col class="mp-l-12"
            >Number of bedrooms: {{ listing.bedroomsUnit5 }}</b-col
          >
        </b-row>
        <b-row v-if="listing.bathroomsUnit5">
          <b-col class="mp-l-12"
            >Number of baths: {{ listing.bathroomsUnit5 }}</b-col
          >
        </b-row>
        <b-row v-if="listing.squareFeetUnit5">
          <b-col class="mp-l-12">Sq. Ft.: {{ listing.squareFeetUnit5 }}</b-col>
        </b-row>
        <b-row v-if="listing.rentUnit5">
          <b-col class="mp-l-12">Monthly rent: {{ listing.rentUnit5 }}</b-col>
        </b-row>
        <b-row v-if="listing.descriptionUnit5">
          <b-col class="mp-l-12"
            >Unit Number: {{ listing.descriptionUnit5 }}</b-col
          >
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.bedroomsUnit6 ||
            listing.bathroomsUnit6 ||
            listing.squareFeetUnit6 ||
            listing.rentUnit6 ||
            listing.descriptionUnit6
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Unit 6 information</span>
        </div>
      </b-row>
      <b-row
        v-if="
          listing.bedroomsUnit6 ||
            listing.bathroomsUnit6 ||
            listing.squareFeetUnit6 ||
            listing.rentUnit6 ||
            listing.descriptionUnit6
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.bedroomsUnit6">
          <b-col class="mp-l-12"
            >Number of bedrooms: {{ listing.bedroomsUnit6 }}</b-col
          >
        </b-row>
        <b-row v-if="listing.bathroomsUnit6">
          <b-col class="mp-l-12"
            >Number of baths: {{ listing.bathroomsUnit6 }}</b-col
          >
        </b-row>
        <b-row v-if="listing.squareFeetUnit6">
          <b-col class="mp-l-12">Sq. Ft.: {{ listing.squareFeetUnit6 }}</b-col>
        </b-row>
        <b-row v-if="listing.rentUnit6">
          <b-col class="mp-l-12">Monthly rent: {{ listing.rentUnit6 }}</b-col>
        </b-row>
        <b-row v-if="listing.descriptionUnit6">
          <b-col class="mp-l-12"
            >Unit Number: {{ listing.descriptionUnit6 }}</b-col
          >
        </b-row>
      </b-row>

      <b-row v-if="listing.interiorFeatures" class="mp-r-5 mp-l-16">
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Interior</span>
        </div>
      </b-row>
      <b-row
        v-if="listing.interiorFeatures"
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-for="item in listing.interiorFeatures.split(',')" :key="item">
          <b-col class="mp-l-12">{{ item }}</b-col>
        </b-row>
      </b-row>

      <b-row v-if="listing.appliancesThatStay" class="mp-r-5 mp-l-16">
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Equipment</span>
        </div>
      </b-row>
      <b-row
        v-if="listing.appliancesThatStay"
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row
          v-for="item in listing.appliancesThatStay.split(',')"
          :key="item"
        >
          <b-col class="mp-l-12">{{ item }}</b-col>
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.fireplacesTotal &&
            (listing.fireplacesMain ||
              listing.fireplacesLower ||
              listing.fireplacesUpper)
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers"
            >Fireplace information</span
          >
        </div>
      </b-row>
      <b-row
        v-if="
          listing.fireplacesTotal &&
            (listing.fireplacesMain ||
              listing.fireplacesLower ||
              listing.fireplacesUpper)
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.fireplacesTotal && listing.fireplacesMain">
          <b-col class="mp-l-12"
            >Number of Fireplaces (main): {{ listing.fireplacesMain }}</b-col
          >
        </b-row>
        <b-row v-if="listing.fireplacesTotal && listing.fireplacesLower">
          <b-col class="mp-l-12"
            >Number of Fireplaces (lower): {{ listing.fireplacesLower }}</b-col
          >
        </b-row>
        <b-row v-if="listing.fireplacesTotal && listing.fireplacesUpper">
          <b-col class="mp-l-12"
            >Number of Fireplaces (upper): {{ listing.fireplacesUpper }}</b-col
          >
        </b-row>
      </b-row>

      <b-row
        v-if="listing.viewsDescription || listing.siteFeatures"
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Property features</span>
        </div>
      </b-row>
      <b-row
        v-if="listing.siteFeatures || listing.viewsDescription"
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <div v-if="listing.viewsDescription" style="float:left;">
          <b-row
            v-for="item in listing.viewsDescription.split(',')"
            :key="item"
          >
            <b-col class="mp-l-12">{{ item }}</b-col>
          </b-row>
        </div>
        <div v-if="listing.siteFeatures" style="float:left;">
          <b-row v-for="item in listing.siteFeatures.split(',')" :key="item">
            <b-col class="mp-l-12">{{ item }}</b-col>
          </b-row>
        </div>
      </b-row>

      <b-row
        v-if="
          listing.energySource ||
            listing.waterSource ||
            listing.powerCompany ||
            listing.waterCompany ||
            listing.sewerCompany ||
            listing.sewer ||
            listing.energyFeatures ||
            listing.electricFeatures ||
            listing.applianceHookups
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Utility information</span>
        </div>
      </b-row>

      <b-row
        v-if="
          listing.energySource ||
            listing.waterSource ||
            listing.powerCompany ||
            listing.waterCompany ||
            listing.sewerCompany ||
            listing.sewer ||
            listing.energyFeatures ||
            listing.electricFeatures ||
            listing.applianceHookups
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <div v-if="listing.energySource" style="float:left;">
          <b-row v-for="item in listing.energySource.split(',')" :key="item">
            <b-col class="mp-l-12">Energy source: {{ item }}</b-col>
          </b-row>
        </div>

        <b-row v-if="listing.waterSource">
          <b-col class="mp-l-12">Water source: {{ listing.waterSource }}</b-col>
        </b-row>
        <b-row v-if="listing.waterCompany">
          <b-col class="mp-l-12"
            >Water company: {{ listing.waterCompany }}</b-col
          >
        </b-row>
        <b-row v-if="listing.sewerCompany">
          <b-col class="mp-l-12"
            >Sewer Company: {{ listing.sewerCompany }}</b-col
          >
        </b-row>
        <b-row v-if="listing.powerCompany">
          <b-col class="mp-l-12"
            >Power company: {{ listing.powerCompany }}</b-col
          >
        </b-row>
        <b-row v-if="listing.sewer">
          <b-col class="mp-l-12">Sewer: {{ listing.sewer }}</b-col>
        </b-row>

        <b-row v-if="listing.energyFeatures">
          <b-col class="mp-l-12"
            >Energy Features: Insulted Windows:
            {{ listing.energyFeatures }}</b-col
          >
        </b-row>
        <b-row v-if="listing.electricFeatures">
          <b-col class="mp-l-12"
            >Electricity Features: {{ listing.electricFeatures }}</b-col
          >
        </b-row>
        <b-row v-if="listing.applianceHookups">
          <b-col class="mp-l-12"
            >Gas Range Connection: {{ listing.applianceHookups }}</b-col
          >
        </b-row>
        <b-row v-if="listing.applianceHookups">
          <b-col class="mp-l-12"
            >Gas Dryer Connection: {{ listing.applianceHookups }}</b-col
          >
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.totalNumberOfParkingSpaces ||
            listing.numberOfGarageSpaces ||
            listing.garageParking
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Parking information</span>
        </div>
      </b-row>

      <b-row
        v-if="
          listing.totalNumberOfParkingSpaces ||
            listing.numberOfGarageSpaces ||
            listing.garageParking
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.totalNumberOfParkingSpaces">
          <b-col class="mp-l-12"
            ># of Parking Spaces:
            {{ listing.totalNumberOfParkingSpaces }}</b-col
          >
        </b-row>
        <b-row v-if="listing.numberOfGarageSpaces">
          <b-col class="mp-l-12"
            ># of Garage Spaces: {{ listing.numberOfGarageSpaces }}</b-col
          >
        </b-row>

        <b-row v-if="listing.garageParking">
          <b-col class="mp-l-12"
            >Attached Garage {{ listing.garageParking }}</b-col
          >
        </b-row>
        <b-row v-if="listing.garageParking">
          <b-col class="mp-l-12"
            >Garage Storage: {{ listing.garageParking }}</b-col
          >
        </b-row>
        <b-row v-if="listing.garageParking">
          <b-col class="mp-l-12"
            >Garage Door Opener: {{ listing.garageParking }}</b-col
          >
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.squareFootFinished ||
            listing.squareFootageUnfinished ||
            listing.entryLocation ||
            listing.documentsProvided ||
            listing.preliminaryTitleOrdered ||
            listing.parcelNumber ||
            listing.project
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Property information</span>
        </div>
      </b-row>
      <b-row
        v-if="
          listing.squareFootFinished ||
            listing.squareFootageUnfinished ||
            listing.entryLocation ||
            listing.documentsProvided ||
            listing.preliminaryTitleOrdered ||
            listing.parcelNumber ||
            listing.project
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.squareFootFinished">
          <b-col class="mp-l-12"
            >Sq. Ft. (Finished):
            {{ numberWithCommas(listing.squareFootFinished) }}</b-col
          >
        </b-row>
        <b-row v-if="listing.squareFootageUnfinished">
          <b-col class="mp-l-12"
            >Sq. Ft. (Unfinished): {{ listing.squareFootageUnfinished }}</b-col
          >
        </b-row>
        <b-row v-if="listing.entryLocation">
          <b-col class="mp-l-12">Entry on {{ listing.entryLocation }}</b-col>
        </b-row>
        <b-row v-if="listing.documentsProvided">
          <b-col class="mp-l-12"
            >Reports/Documents completed: {{ listing.documentsProvided }}</b-col
          >
        </b-row>
        <b-row v-if="listing.preliminaryTitleOrdered">
          <b-col class="mp-l-12"
            >Preliminary title ordered:
            {{ listing.preliminaryTitleOrdered == "Y" ? "Yes" : "No" }}</b-col
          >
        </b-row>
        <b-row v-if="listing.parcelNumber">
          <b-col class="mp-l-12"
            >Tax ID number: {{ listing.parcelNumber }}</b-col
          >
        </b-row>
        <b-row v-if="listing.project">
          <b-col class="mp-l-12">Plat/Subdivision: {{ listing.project }}</b-col>
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.parkingType ||
            listing.coveredParkingTotal ||
            listing.parkingSpaceNumber ||
            listing.totalNumberOfParkingSpaces ||
            listing.numberOfGarageSpaces ||
            listing.numberOfCarportSpaces ||
            listing.numberOfUncoveredSpaces ||
            listing.numberOfAssignedSpaces ||
            listing.unitFloorNumber
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Parking information</span>
        </div>
      </b-row>
      <b-row
        v-if="
          listing.parkingType ||
            listing.coveredParkingTotal ||
            listing.parkingSpaceNumber ||
            listing.totalNumberOfParkingSpaces ||
            listing.numberOfGarageSpaces ||
            listing.numberOfCarportSpaces ||
            listing.numberOfUncoveredSpaces ||
            listing.numberOfAssignedSpaces ||
            listing.unitFloorNumber
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.parkingType">
          <b-col class="mp-l-12">{{ listing.parkingType }}</b-col>
        </b-row>
        <b-row v-if="listing.coveredParkingTotal">
          <b-col class="mp-l-12"
            >Total covered parking: {{ listing.coveredParkingTotal }}</b-col
          >
        </b-row>
        <b-row v-if="listing.parkingSpaceNumber">
          <b-col class="mp-l-12"
            >Parking space number: {{ listing.parkingSpaceNumber }}</b-col
          >
        </b-row>
        <b-row v-if="listing.totalNumberOfParkingSpaces">
          <b-col class="mp-l-12"
            >Number of garking gpaces (Total):
            {{ listing.totalNumberOfParkingSpaces }}</b-col
          >
        </b-row>
        <b-row v-if="listing.numberOfGarageSpaces">
          <b-col class="mp-l-12"
            >Number of garage gpaces: {{ listing.numberOfGarageSpaces }}</b-col
          >
        </b-row>
        <b-row v-if="listing.numberOfCarportSpaces">
          <b-col class="mp-l-12"
            >Number of garport gpaces:
            {{ listing.numberOfCarportSpaces }}</b-col
          >
        </b-row>
        <b-row v-if="listing.numberOfUncoveredSpaces">
          <b-col class="mp-l-12"
            >Number of gncovered gpaces:
            {{ listing.numberOfUncoveredSpaces }}</b-col
          >
        </b-row>
        <b-row v-if="listing.numberOfAssignedSpaces">
          <b-col class="mp-l-12"
            >Number of assigned parking spaces:
            {{ listing.numberOfAssignedSpaces }}</b-col
          >
        </b-row>
        <b-row v-if="listing.unitFloorNumber">
          <b-col class="mp-l-12"
            >Floor number of unit: {{ listing.unitFloorNumber }}</b-col
          >
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.occupied ||
            listing.architecture ||
            listing.numberOfStoriesInBuilding ||
            listing.numberOfUnitsInBuilding ||
            listing.unitFloorNumber ||
            listing.buildingCondition ||
            listing.storageNumber ||
            listing.storageLocation ||
            listing.effectiveYearBuilt ||
            listing.builder ||
            listing.EPSEnergy ||
            listing.HERSIndex ||
            listing.foundation
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Building information</span>
        </div>
      </b-row>
      <b-row
        v-if="
          listing.occupied ||
            listing.architecture ||
            listing.buildingInformation ||
            listing.numberOfStoriesInBuilding ||
            listing.numberOfUnitsInBuilding ||
            listing.unitFloorNumber ||
            listing.buildingCondition ||
            listing.storageNumber ||
            listing.storageLocation ||
            listing.effectiveYearBuilt ||
            listing.builder ||
            listing.EPSEnergy ||
            listing.HERSIndex ||
            listing.foundation
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.occupied">
          <b-col class="mp-l-12"> {{ listing.occupied }}</b-col>
        </b-row>
        <b-row v-if="listing.architecture">
          <b-col class="mp-l-12"> {{ listing.architecture }}</b-col>
        </b-row>
        <b-row v-if="listing.buildingInformation">
          <b-col class="mp-l-12"> {{ listing.buildingInformation }}</b-col>
        </b-row>
        <b-row v-if="listing.numberOfStoriesInBuilding">
          <b-col class="mp-l-12"
            >Number of stories in building:
            {{ listing.numberOfStoriesInBuilding }}</b-col
          >
        </b-row>
        <b-row v-if="listing.foundation">
          <b-col class="mp-l-12">{{ listing.foundation }} Foundation</b-col>
        </b-row>
        <b-row v-if="listing.numberOfUnitsInBuilding">
          <b-col class="mp-l-12"
            >Number of units: {{ listing.numberOfUnitsInBuilding }}</b-col
          >
        </b-row>
        <b-row v-if="listing.unitFloorNumber">
          <b-col class="mp-l-12"
            >Total floors: {{ listing.unitFloorNumber }}</b-col
          >
        </b-row>
        <b-row v-if="listing.buildingCondition">
          <b-col class="mp-l-12"
            >Building condition: {{ listing.buildingCondition }}</b-col
          >
        </b-row>
        <b-row v-if="listing.storageNumber">
          <b-col class="mp-l-12"
            >Storage number: {{ listing.storageNumber }}</b-col
          >
        </b-row>
        <b-row v-if="listing.storageLocation">
          <b-col class="mp-l-12"
            >Storage location: {{ listing.storageLocation }}</b-col
          >
        </b-row>
        <b-row v-if="listing.effectiveYearBuilt">
          <b-col class="mp-l-12"
            >Effective Year Built Source:
            {{ listing.effectiveYearBuilt }}</b-col
          >
        </b-row>
        <b-row v-if="listing.builder">
          <b-col class="mp-l-12">Builder name: {{ listing.builder }}</b-col>
        </b-row>
        <b-row v-if="listing.EPSEnergy">
          <b-col class="mp-l-12"
            >EPS energy score: {{ listing.EPSEnergy }}</b-col
          >
        </b-row>
        <b-row v-if="listing.HERSIndex">
          <b-col class="mp-l-12"
            >HERS index score: {{ listing.HERSIndex }}</b-col
          >
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.schools &&
            (listing.schoolDistrictCode ||
              listing.schools.elementary ||
              listing.schools.juniorHigh ||
              listing.schools.seniorHigh)
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">School information</span>
        </div>
      </b-row>
      <b-row
        v-if="
          listing.schools &&
            (listing.schoolDistrictCode ||
              listing.schools.elementary ||
              listing.schools.juniorHigh ||
              listing.schools.seniorHigh)
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.schools.elementary">
          <b-col class="mp-l-12"
            >Elementary School: {{ listing.schools.elementary.name }}</b-col
          >
        </b-row>
        <b-row v-if="listing.schools.juniorHigh">
          <b-col class="mp-l-12"
            >Junior High School: {{ listing.schools.juniorHigh.name }}</b-col
          >
        </b-row>
        <b-row v-if="listing.schools.seniorHigh">
          <b-col class="mp-l-12"
            >Senior High School: {{ listing.schools.seniorHigh.name }}</b-col
          >
        </b-row>
        <b-row v-if="listing.schools.seniorHigh">
          <b-col class="mp-l-12"
            >School district: {{ listing.schoolDistrictCode }}
          </b-col>
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.potentialTerms ||
            listing.taxAmount ||
            listing.taxYear ||
            listing.seniorExemption
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers"
            >Financial information</span
          >
        </div>
      </b-row>
      <b-row
        v-if="
          listing.potentialTerms ||
            listing.taxAmount ||
            listing.taxYear ||
            listing.seniorExemption
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <div v-if="listing.potentialTerms">
          <b-row v-for="item in listing.potentialTerms.split(',')" :key="item">
            <b-col class="mp-l-12">{{ item }}</b-col>
          </b-row>
        </div>
        <b-row v-if="listing.taxAmount">
          <b-col class="mp-l-12"
            >Annual taxes: ${{ numberWithCommas(listing.taxAmount) }}</b-col
          >
        </b-row>
        <b-row v-if="listing.taxYear">
          <b-col class="mp-l-12">Tax year: {{ listing.taxYear }}</b-col>
        </b-row>
        <b-row v-if="listing.seniorExemption == 'N'">
          <b-col class="mp-l-12">No Senior Exemption</b-col>
        </b-row>
        <b-row v-if="listing.seniorExemption != 'N'">
          <b-col class="mp-l-12">Senior Exemption</b-col>
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.zoningJurisdiction ||
            listing.zoningCode ||
            listing.generalZoningClassification ||
            listing.lotNumber ||
            listing.lotDimensions ||
            listing.taxParcelLetter ||
            listing.lotSquareFoot
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers"
            >Property / lot details</span
          >
        </div>
      </b-row>
      <b-row
        v-if="
          listing.zoningJurisdiction ||
            listing.zoningCode ||
            listing.generalZoningClassification ||
            listing.lotNumber ||
            listing.lotDimensions ||
            listing.taxParcelLetter ||
            listing.lotSquareFoot
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.zoningJurisdiction">
          <b-col class="mp-l-12"
            >Zoning jurisdiction: {{ listing.zoningJurisdiction }}</b-col
          >
        </b-row>
        <b-row v-if="listing.zoningCode">
          <b-col class="mp-l-12">Zoning code: {{ listing.zoningCode }}</b-col>
        </b-row>
        <b-row v-if="listing.generalZoningClassification">
          <b-col class="mp-l-12"
            >General zoning class:
            {{ listing.generalZoningClassification }}</b-col
          >
        </b-row>
        <b-row v-if="listing.lotNumber">
          <b-col class="mp-l-12">Lot number: {{ listing.lotNumber }}</b-col>
        </b-row>
        <b-row v-if="listing.lotDimensions">
          <b-col class="mp-l-12">Dimensions: {{ listing.lotDimensions }}</b-col>
        </b-row>
        <b-row v-if="listing.lotSquareFoot">
          <b-col class="mp-l-12"
            >Lot Size (Sq. Ft.): {{ listing.lotSquareFoot }}</b-col
          >
        </b-row>
        <b-row v-if="listing.taxParcelLetter">
          <b-col class="mp-l-12">Parcel #: {{ listing.taxParcelLetter }}</b-col>
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.lotDetails ||
            listing.roadOnWhichSideOfProperty ||
            listing.soilsFeasibilityAvailable ||
            listing.easements ||
            listing.assessmentFees ||
            listing.quarter ||
            listing.roadInformation
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Lot/Land information</span>
        </div>
      </b-row>

      <b-row
        v-if="
          listing.lotDetails ||
            listing.roadOnWhichSideOfProperty ||
            listing.soilsFeasibilityAvailable ||
            listing.easements ||
            listing.assessmentFees ||
            listing.quarter ||
            listing.roadInformation
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <div v-if="listing.lotDetails">
          <b-row v-for="item in listing.lotDetails.split(',')" :key="item">
            <b-col class="mp-l-12">{{ item }}</b-col>
          </b-row>
        </div>
        <b-row v-if="listing.roadOnWhichSideOfProperty">
          <b-col class="mp-l-12"
            >Zoning jurisdiction: {{ listing.roadOnWhichSideOfProperty }}</b-col
          >
        </b-row>
        <b-row v-if="listing.soilsFeasibilityAvailable">
          <b-col class="mp-l-12"
            >Zoning code: {{ listing.soilsFeasibilityAvailable }}</b-col
          >
        </b-row>
        <b-row v-if="listing.easements">
          <b-col class="mp-l-12">Easements: {{ listing.easements }}</b-col>
        </b-row>
        <b-row v-if="listing.assessmentFees">
          <b-col class="mp-l-12"
            >Assessment fees: {{ listing.assessmentFees }}</b-col
          >
        </b-row>
        <b-row v-if="listing.quarter">
          <b-col class="mp-l-12">Quarter: {{ listing.quarter }}</b-col>
        </b-row>

        <b-row v-if="listing.pool" class="mp-r-5 mp-l-16">
          <div class="col-md-12 pl-0 pr-0">
            <span class="theme-font-semi-bold headers">Pool information</span>
          </div>
        </b-row>
        <b-row v-if="listing.pool" class="kd theme-font-light mp-r-5 mp-l-5">
          <b-row v-for="item in listing.pool.split(',')" :key="item">
            <b-col class="mp-l-12">{{ item }}</b-col>
          </b-row>
        </b-row>

        <b-row v-if="listing.roadInformation">
          <b-col class="mp-l-12"
            >Road Type : {{ listing.roadInformation }}</b-col
          >
        </b-row>
      </b-row>

      <b-row
        v-if="listing.block || listing.mmapBook || listing.busRouteNumber"
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Location details</span>
        </div>
      </b-row>

      <b-row
        v-if="listing.block || listing.mmapBook || listing.busRouteNumber"
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.block">
          <b-col class="mp-l-12">Block: {{ listing.block }}</b-col>
        </b-row>
        <b-row v-if="listing.mmapBook">
          <b-col class="mp-l-12">Map book: {{ listing.mmapBook }}</b-col>
        </b-row>
        <b-row v-if="listing.busRouteNumber">
          <b-col class="mp-l-12"
            >Bus route number: {{ listing.busRouteNumber }}</b-col
          >
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.hoa ||
            (listing.monthlyRentIfRented && listing.monthlyRentIfRented != 0) ||
            listing.specialAssessmentAmount ||
            listing.homeOwnerDuesInclude
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers"
            >Home owners association</span
          >
        </div>
      </b-row>

      <b-row
        v-if="
          listing.hoa ||
            (listing.monthlyRentIfRented && listing.monthlyRentIfRented != 0) ||
            listing.specialAssessmentAmount ||
            listing.homeOwnerDuesInclude
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.hoa">
          <b-col class="mp-l-12">Dues ($): {{ listing.hoa }}</b-col>
        </b-row>
        <b-row
          v-if="listing.monthlyRentIfRented && listing.monthlyRentIfRented != 0"
        >
          <b-col class="mp-l-12"
            >Monthly Rent ($): {{ listing.monthlyRentIfRented }}</b-col
          >
        </b-row>
        <b-row v-if="listing.specialAssessmentAmount">
          <b-col class="mp-l-12"
            >Spec. Assessment Amt ($):
            {{ listing.specialAssessmentAmount }}</b-col
          >
        </b-row>
        <b-row v-if="listing.homeOwnerDuesInclude">
          <b-row
            v-for="item in listing.homeOwnerDuesInclude.split(',')"
            :key="item"
          >
            <b-col class="mp-l-12">{{ item }}</b-col>
          </b-row>
        </b-row>
      </b-row>

      <b-row
        v-if="
          listing.manufacturedHomeSerialNumber ||
            listing.propertyType ||
            listing.offers ||
            listing.possession ||
            listing.agentCommission ||
            listing.disclosure ||
            listing.disclaimer ||
            listing.exclusions ||
            listing.lenderOwned ||
            listing.shortSaleWithApptReqd
        "
        class="mp-r-5 mp-l-16"
      >
        <div class="col-md-12 pl-0 pr-0">
          <span class="theme-font-semi-bold headers">Listing information</span>
        </div>
      </b-row>

      <b-row
        v-if="
          listing.manufacturedHomeSerialNumber ||
            listing.propertyType ||
            listing.offers ||
            listing.possession ||
            listing.agentCommission ||
            listing.disclosure ||
            listing.disclaimer ||
            listing.exclusions ||
            listing.lenderOwned ||
            listing.shortSaleWithApptReqd
        "
        class="kd theme-font-light mp-r-5 mp-l-5"
      >
        <b-row v-if="listing.manufacturedHomeSerialNumber">
          <b-col class="mp-l-12"
            >Manufactured homes:
            {{ listing.manufacturedHomeSerialNumber }}</b-col
          >
        </b-row>
        <b-row v-if="listing.propertyType">
          <b-row v-for="item in listing.propertyType.split(',')" :key="item">
            <b-col class="mp-l-12">{{ item }}</b-col>
          </b-row>
        </b-row>
        <b-row v-if="listing.offers">
          <b-row v-for="item in listing.offers.split(',')" :key="item">
            <b-col class="mp-l-12">{{ item }}</b-col>
          </b-row>
        </b-row>
        <b-row v-if="listing.possession">
          <b-col class="mp-l-12">Possession: {{ listing.possession }}</b-col>
        </b-row>
        <b-row v-if="listing.agentCommission" style="display:none">
          <b-col class="mp-l-12"
            >Buyer's agent commission: {{ listing.agentCommission }}%</b-col
          >
        </b-row>

        <b-row v-if="listing.disclosure">
          <b-col class="mp-l-12"
            >No Disclosures: {{ listing.disclosure }}%</b-col
          >
        </b-row>
        <b-row v-if="listing.disclaimer">
          <b-col class="mp-l-12">Disclosures: {{ listing.disclaimer }}%</b-col>
        </b-row>
        <b-row v-if="listing.exclusions">
          <b-col class="mp-l-12">Exclusions: {{ listing.exclusions }}%</b-col>
        </b-row>
        <b-row v-if="listing.lenderOwned">
          <b-col class="mp-l-12"
            >Lender Owned: {{ listing.lenderOwned }}%</b-col
          >
        </b-row>
        <b-row v-if="listing.shortSaleWithApptReqd">
          <b-col class="mp-l-12"
            >Short Sale Lender Approval Required:
            {{ listing.shortSaleWithApptReqd }}%</b-col
          >
        </b-row>
      </b-row>

      <b-row class="mp-r-5 mp-l-16">
        <p
          style="color: #57c1a6;font-size: 18px;cursor: pointer;"
          @click="showfullDetails = false"
        >
          Hide full details
        </p>
        <hr />
      </b-row>
    </div>
    <b-row class="mp-r-5 mp-l-16">
      <div v-if="!showfullDetails">
        <p
          style="color: #57c1a6;font-size: 18px;cursor: pointer;"
          @click="showfullDetails = true"
        >
          Show full details
        </p>
      </div>
    </b-row>
    <!-- End of code -->

    <b-row class="kd theme-font-light mp-r-5 mp-l-16">
      <span class="theme-font-semi-bold headers">Upcoming Open Houses</span>
    </b-row>
    <div v-if="!isEmpty(listing.openHouses)" class="row">
      <div class="col-md-12 mp-r-5 mp-l-16">
        <div
          v-for="(openHouse, index) in listing.openHouses"
          :key="index"
          class="kd theme-font-light d-flex align-items-center mt-1"
          :class="openHouse.type === 'VIR' ? 'livestream' : ''"
        >
          <div v-if="openHouse.type === 'VIR'" class="pl-0">
            <div
              class="live-text p-click"
              @click.prevent="liveStreamJoin(openHouse)"
            >
              LIVE STREAM
            </div>
          </div>
          <div v-else class="pl-0">
            <img src="@/assets/icons/calendar_icon.svg" />
          </div>
          <div class="pl-1">
            <span
              class="kd open-hours fw-500"
              v-html="openHours(openHouse.open, openHouse.close)"
            ></span>
          </div>
          <div v-if="openHouse.type === 'VIR'" class="joinLnk">
            <a
              href="#"
              class="link fw-600"
              @click.prevent="liveStreamJoin(openHouse)"
              >Join</a
            >
          </div>
        </div>
      </div>
    </div>
    <b-row v-else class="kd theme-font-light mp-r-5 mp-l-16">
      <b-col class="pl-0" cols="1">
        <img src="@/assets/icons/calendar_icon.svg" />
      </b-col>
      <b-col class="pl-0">
        <span class="kd theme-font-light">No upcoming open houses</span>
      </b-col>
    </b-row>
    <br />
    <b-row class="theme-font-regular stats pt-3 mp-r-5 mp-l-16">
      <div class="col-md-12 pl-0">
        <!-- Open houses -->
        <span class="fix-space"
          >We have agents available INSTANTLY to show this home.&nbsp;</span
        >
        <span class="secondarycolor theme-font-semi-bold fix-space"
          >${{ numberWithCommas(listing.cashBack.toFixed(2)) }}</span
        >
        <span class="fix-space">&nbsp;money back to you with our&nbsp;</span>
        <span class="secondarycolor theme-font-semi-bold fix-space"
          >{{ listing.agentCommission / 2 }}% buyer rebate.</span
        >
      </div>
      <div class="col-md-12 pl-0 mt-3">
        <b>{{ getAgentCount }} Agents</b> &lt; {{ getTimeDistance }} minutes
        away
      </div>
    </b-row>
    <b-row>
      <b-col class="pl-0" md="12">
        <hr />
      </b-col>
    </b-row>
    <!--Mortgage Calc -->
    <b-row class="kd theme-font-light">
      <b-col cols="12" class="propPrice">
        <div class="font-bold-2 fw-600 font-20 color-dark">
          ${{ numberWithCommas(monthlyPayment) }}&nbsp;&nbsp;per month&nbsp;
        </div>
        <span class="">{{ mortgageYears }}-Year Fixed</span>
        <span>
          {{ interestRate }}% Interest
          <img
            v-b-popover.hover.top="interestInfo"
            src="@/assets/icons/info.svg"
            :title="
              mortgageYears + '-Year Fixed, ' + interestRate + '% Interest'
            "
            variant="primary"
          />
        </span>
        <!-- , ${{ downAmount }} Down -->
      </b-col>
      <b-col cols="12" class="mt-3">
        <b-progress :max="max" class="mb-3">
          <b-progress-bar
            v-model="progressBarVals.loan"
            variant="info"
          ></b-progress-bar>
          <b-progress-bar
            v-model="progressBarVals.pTax"
            variant="warning"
          ></b-progress-bar>
          <b-progress-bar
            v-model="progressBarVals.insurance"
            variant="success"
          ></b-progress-bar>
          <b-progress-bar
            v-model="progressBarVals.hoa"
            variant="danger"
          ></b-progress-bar>
          <b-progress-bar
            v-if="downPercent < 20"
            v-model="progressBarVals.mortgageInsurance"
            variant=""
            class="bg-dark-green"
          ></b-progress-bar>
        </b-progress>
      </b-col>
      <b-row>
        <b-col md="6">
          <div class="d-flex justify-content-between">
            <div class="circle-point sky-blue"></div>
            <div class="text-nowrap flex-grow-1 pl-2">
              Principal and Interest
            </div>
            <div class="fw-500">
              ${{ numberWithCommas(principalInterestMonthly.toFixed(2)) }}&nbsp;
            </div>
          </div>
        </b-col>
        <b-col md="6" class="">
          <div class="d-flex justify-content-between">
            <div class="circle-point orange"></div>
            <div class="flex-grow-1 pl-2">
              Property Taxes
            </div>
            <div class="fw-500">${{ monthlyTax }}&nbsp;</div>
          </div>
        </b-col>
      </b-row>
      <b-row class="hide-in-mobile">
        <b-col class="pl-0 pr-0 mr-0 ml-0" md="12">
          <hr class="mt-2 mb-2" />
        </b-col>
      </b-row>
      <b-row>
        <b-col md="6">
          <div class="d-flex justify-content-between">
            <div class="circle-point green"></div>
            <div class="flex-grow-1 pl-2">
              Home Insurance
            </div>
            <div class="fw-500">${{ homeInsurance }}</div>
          </div>
        </b-col>
        <b-col md="6">
          <div class="d-flex">
            <div class="circle-point red"></div>
            <div class="flex-grow-1 pl-2">
              HOA
            </div>
            <div class="fw-500">${{ numberWithCommas(hoaDs) }}</div>
          </div>
        </b-col>
      </b-row>
      <b-row v-if="downPercent < 20" class="hide-in-mobile">
        <b-col class="pl-0 pr-0 mr-0 ml-0" md="12">
          <hr class="mt-2 mb-2" />
        </b-col>
      </b-row>
      <div v-if="downPercent < 20" class="row">
        <div class="col-12 col-md-6 col-xl-6 col-sm-6">
          <div class="d-flex justify-content-between">
            <div class="circle-point dark-green"></div>
            <div class="flex-grow-1 pl-2">
              Mortgage Insurance
            </div>
            <div class="fw-500">${{ numberWithCommas(mortgageInsurance) }}</div>
          </div>
        </div>
      </div>
      <b-col class="pl-0 pr-0" md="12"> &ensp; <br /> </b-col>
      <b-col v-show="showMortgageForm" cols="12" class="mortgageCalculator">
        <b-form @submit.prevent="calculateMortgage()">
          <b-row class="my-1">
            <b-col cols="12">
              <b-form-group class="" label="Home Price" label-for="home_price">
                <b-input-group prepend="$">
                  <b-form-input
                    id="home_price"
                    v-model="homePriceValue"
                    @keypress="onlyNumber"
                    @focus="inputFocusOn($event, 'homePrice')"
                    @focusout="inputFocusOut($event, 'homePriceValue')"
                    @blur="changeHomePriceAmount($event)"
                  ></b-form-input>
                </b-input-group>
              </b-form-group>
            </b-col>
            <b-col cols="12">
              <label for="cash_down">Down payment</label>
              <div class="input-group fourSide">
                <div class="input-group-prepend">
                  <span class="input-group-text">$</span>
                </div>
                <input
                  id="cash_down"
                  v-model="downAmountValue"
                  type="text"
                  class="form-control"
                  @keypress="onlyNumber"
                  @focus="inputFocusOn($event, 'downPayment')"
                  @focusout="inputFocusOut($event, 'downAmountValue')"
                />
                <input
                  v-model="downPercent"
                  type="text"
                  class="form-control cashDownPercent"
                  @keyup="changeDPPercentage()"
                />
                <div class="input-group-prepend">
                  <span class="input-group-text">%</span>
                </div>
              </div>

              <b-form-group class="">
                <div class="range-input my-3">
                  <b-form-input
                    id="cash_down_range"
                    v-model="downPercent"
                    class="inp-range"
                    type="range"
                    min="0"
                    max="100"
                    @change="changeDPPercentage()"
                  ></b-form-input>
                  <div
                    class="range-output"
                    :style="{ width: downPercent + '%' }"
                  ></div>
                </div>
              </b-form-group>
            </b-col>
            <b-col cols="12">
              <label for="PropertyTaxes">Property Taxes</label>
              <div class="input-group fiveSide mb-4">
                <div class="input-group-prepend">
                  <span class="input-group-text">$</span>
                </div>
                <input
                  id="PropertyTaxes"
                  v-model="propertyTaxAmount"
                  type="text"
                  class="form-control"
                  @blur="changePropertyTaxes($event)"
                  @focusout="inputFocusOut($event, 'propertyTaxAmount')"
                  @focus="inputFocusOn($event, 'PropertyTaxes')"
                />
                <div class="input-group-prepend">
                  <span class="input-group-text">/year</span>
                </div>
                <input
                  v-model="propertyTaxesPercent"
                  type="text"
                  class="form-control"
                  @focus="prpTaxPecFocused($event)"
                  @blur="changePropertyTaxesPercent($event)"
                />
                <div class="input-group-append">
                  <span class="input-group-text">%</span>
                </div>
              </div>
            </b-col>

            <b-col cols="12">
              <b-form-group
                class="mb-0 threeSide mb-4"
                label="HOA Dues"
                label-for="hoa_dues"
              >
                <b-input-group id="hoa_dues" prepend="$" append="/month">
                  <b-form-input
                    id="hoa_dues"
                    v-model="hoaDues"
                    placeholser="HOA DUES"
                  ></b-form-input>
                </b-input-group>
              </b-form-group>
            </b-col>

            <b-col cols="12">
              <label for="home_insurance">Home Insurance</label>
              <div class="input-group fiveSide mb-4">
                <div class="input-group-prepend">
                  <span class="input-group-text">$</span>
                </div>
                <input
                  id="home_insurance"
                  v-model="homeInsurance"
                  type="text"
                  class="form-control"
                  @focus="
                    inputFocusOn($event);
                    homeIncFocused($event);
                  "
                  @blur="homeInsuranceChange($event)"
                />
                <div class="input-group-prepend">
                  <span class="input-group-text">/month</span>
                </div>
                <input
                  v-model="homeInsurancePercent"
                  type="text"
                  class="form-control"
                  @focus="
                    inputFocusOn($event);
                    hipFocused($event);
                  "
                  @blur="homeInsurancePercentChange($event)"
                />
                <div class="input-group-append">
                  <span class="input-group-text">%</span>
                </div>
              </div>
            </b-col>

            <b-col v-if="downPercent < 20" cols="12">
              <label for="home_insurance">Mortgage Insurance</label>
              <div class="input-group fiveSide mb-4">
                <div class="input-group-prepend">
                  <span class="input-group-text">$</span>
                </div>
                <input
                  id="home_insurance"
                  v-model="mortgageInsurance"
                  type="text"
                  class="form-control"
                  @focus="
                    inputFocusOn($event);
                    mortgageInsuranceFocused($event);
                  "
                  @blur="mortgageInsuranceChange($event)"
                />
                <div class="input-group-prepend">
                  <span class="input-group-text">/month</span>
                </div>
                <input
                  v-model="mortgageInsurancePercentage"
                  type="text"
                  class="form-control"
                  @focus="inputFocusOn($event)"
                  @blur="mortgageInsurancePercentChange($event)"
                />
                <div class="input-group-append">
                  <span class="input-group-text">%</span>
                </div>
              </div>
            </b-col>
          </b-row>
          <div class="mb-4">
            <b-row class="interest-rate bg-base my-3">
              <b-col cols="12">
                <label for="interest_rate">Interest rate</label>
                <div class="input-group mb-3">
                  <div class="input-group-prepend">
                    <span id="interest_rate" class="input-group-text">%</span>
                  </div>
                  <input
                    v-model="interestRate"
                    type="text"
                    class="form-control"
                    aria-describedby="basic-addon1"
                    @focus="inputFocusOn($event)"
                    @blur="updateInterestRate($event)"
                  />
                </div>
              </b-col>
              <b-col cols="12" class="mt-4">
                <label
                  >Loan type
                  <img
                    v-b-popover.hover.top="loanType"
                    src="@/assets/icons/info.svg"
                    title="Loan type"
                    variant="primary"
                /></label>
                <b-dropdown class="dropDownItems">
                  <template v-slot:button-content>
                    {{ mortgageText }}
                  </template>

                  <b-dropdown-item
                    v-for="Nm in mortgageYearsOptions"
                    :key="Nm.value"
                    href="#"
                    @click.prevent="mortgageChange(Nm.value, Nm.text)"
                    >{{ Nm.text }}</b-dropdown-item
                  >
                </b-dropdown>
              </b-col>
              <b-col cols="12" class="mt-4">
                <label>Credit Score</label>
                <b-dropdown class="dropDownItems">
                  <template v-slot:button-content>
                    {{ creditScoreText }}
                  </template>

                  <b-dropdown-item
                    v-for="Nm in creditScoreOptions"
                    :key="Nm.value"
                    href="#"
                    @click.prevent="creditScoreChange(Nm.value, Nm.text)"
                    >{{ Nm.text }}</b-dropdown-item
                  >
                </b-dropdown>
              </b-col>
            </b-row>
          </div>
          <div class="row">
            <div class="col-md-12 text-center bg-base  pt-1 pb-1 mb-3">
              <b-button
                type="button"
                class="btn-link saveMortgageCalc font-14"
                @click="resetCalculator"
                >Reset calculations
              </b-button>
            </div>
          </div>
          <b-row class="mt-4">
            <div class="col-6 pl-0 justify-content-center align-items-center">
              <b-button type="submit" class="btn-link saveMortgageCalc"
                >Save custom calculation
              </b-button>
            </div>
            <div class="col-6 pr-0 text-right m-pl-0 m-pr-0">
              <b-button
                pill
                variant="dark-2"
                class="connectLander"
                @click="connectWithLender(listing)"
                >Connect with a lender</b-button
              >
            </div>
          </b-row>
        </b-form>
      </b-col>
      <b-col v-if="!showMortgageForm" cols="12" class="pl-0 pr-0">
        <b-row>
          <div class="col-md-6 col-sm-6 col-6">
            <a
              href="#"
              class="btn-link mortgageCalc"
              title="Mortgage Calculator"
              @click.prevent="toggleForm()"
            >
              {{ " Mortgage Calculator" }}
            </a>
          </div>
          <div class="col-md-6 col-sm-6 col-6 m-pl-0 m-pr-0">
            <b-button
              pill
              variant="dark-2"
              class="connectLander"
              @click="connectWithLender(listing)"
              >Connect with a lender</b-button
            >
          </div>
        </b-row>
      </b-col>
    </b-row>
    <div class="row">
      <b-col class="pl-0 pr-0" md="12">
        <hr />
      </b-col>
    </div>
    <!--Prop history -->
    <b-row class="kd theme-font-light">
      <b-col md="12">
        <span class="theme-font-semi-bold headers fw-600">
          Property history
        </span>
      </b-col>
    </b-row>
    <div v-if="listing.listingHistory.length !== 0" class="row">
      <b-row
        v-for="(ls, index) in sortHistory"
        :key="index"
        class="kd theme-font-light propertyHistory"
        :class="{ hideContent: 'hide-content' }"
      >
        <!-- v-if="index > 5 ? (hideContent = true) : ''" -->
        <b-col>
          <p>
            {{
              new Date(ls.changeDate).toLocaleDateString("en-US", {
                year: "numeric",
                month: "long",
                day: "numeric"
              })
            }}
          </p>
          <p>Price Change</p>
          <p>NWMLS #{{ listing.mls }}</p>
        </b-col>
        <b-col>
          <p class="color-red">
            ${{
              numberWithCommas(priceChange(ls.listPrice, listing.listingPrice))
            }}
          </p>
          <p>${{ numberWithCommas(ls.listPrice) }}</p>
          <p>&nbsp;</p>
        </b-col>
        <b-col class="ml-0 mr-0 pl-0 pr-0" md="12">
          <hr />
        </b-col>
      </b-row>
    </div>
    <div v-else>
      <div class="row">
        <div class="col-md-12">
          <p class="font-14 mt-2">No property history found.</p>
        </div>
      </div>
    </div>
    <b-row class="kd theme-font-light">
      <b-col md="12">
        <a
          title="View more property history"
          href="#"
          class="theme-font-semi-bold btn-link font-14"
          @click="!hideContent"
          >View more property history</a
        >
      </b-col>
    </b-row>
    <b-row>
      <b-col md="12" class="pl-0 pr-0">
        <hr />
      </b-col>
    </b-row>
    <!--Schools -->
    <div class="row">
      <div class="col-md-12">
        <p class="headers fw-600">Schools</p>
      </div>
    </div>
    <b-row class="kd theme-font-light mp-r-5 mp-l-5">
      <b-col class="pl-0 pr-0">
        <b-tabs
          class="schoolDetails"
          content-class="mt-3"
          fill
          style="width:100%;"
        >
          <b-tab title="All" active style="width:100%;">
            <div v-if="!isEmpty(listing.schools)">
              <div
                v-for="(school, index) in listing.schools"
                :key="index"
                style="width:100%;"
              >
                <b-row class="schoolList" :class="school ? '' : 'no-school'">
                  <b-col v-if="school" md="6" sm="8" cols="8" class="pl-0">
                    <p class="text-nowrap" :title="school.name">
                      {{ school.name }}
                    </p>
                    <span class="d-sm-none">2 miles away</span>
                    <p>Public grades {{ school.grade_Range }}</p>
                  </b-col>
                  <b-col v-if="school" md="3" class="d-none d-md-block">
                    <span>2 miles away</span>
                  </b-col>
                  <b-col
                    v-if="school"
                    md="3"
                    sm="4"
                    cols="4"
                    class="text-right"
                  >
                    <span>Rated {{ school.rating }}/10</span>
                  </b-col>
                </b-row>
              </div>
            </div>
          </b-tab>
          <b-tab title="Elementary" style="width:100%;">
            <b-row
              v-if="listing.schools.elementary !== null"
              class="schoolList"
            >
              <b-col md="6" sm="8" cols="8" class="pl-0">
                <p class="text-nowrap" :title="listing.schools.elementary.name">
                  {{ listing.schools.elementary.name }}
                </p>
                <p>
                  Public grades {{ listing.schools.elementary.grade_Range }}
                </p>
              </b-col>
              <b-col md="3" class="d-none d-md-block">
                <p>2 miles away</p>
              </b-col>
              <b-col md="3" sm="4" cols="4" class="text-right">
                <p>Rated {{ listing.schools.elementary.rating }}/10</p>
              </b-col>
            </b-row>
            <b-row v-else>
              This district has no Elementary school
              <b-col md="12" class="pl-0 pr-0">
                <hr />
              </b-col>
            </b-row>
          </b-tab>
          <b-tab title="Middle" style="width:100%;">
            <b-row
              v-if="listing.schools.juniorHigh !== null"
              class="schoolList"
            >
              <b-col md="6" sm="8" cols="8" class="pl-0">
                <p class="text-nowrap" :title="listing.schools.juniorHigh.name">
                  {{ listing.schools.juniorHigh.name }}
                </p>
                <p>
                  Public grades {{ listing.schools.juniorHigh.grade_Range }}
                </p>
              </b-col>
              <b-col md="3" class="d-none d-md-block">
                <p>2 miles away</p>
              </b-col>
              <b-col md="3" sm="4" cols="4" class="text-right">
                <p>Rated {{ listing.schools.juniorHigh.rating }}/10</p>
              </b-col>
            </b-row>

            <b-row v-else>
              This district has no Middle school
              <b-col md="12" class="pl-0 pr-0">
                <hr />
              </b-col>
            </b-row>
          </b-tab>
          <b-tab title="High" style="width:100%;">
            <b-row
              v-if="listing.schools.seniorHigh !== null"
              class="schoolList"
            >
              <b-col md="6" sm="8" cols="8" class="pl-0">
                <p class="text-nowrap" :title="listing.schools.seniorHigh.name">
                  {{ listing.schools.seniorHigh.name }}
                </p>
                <p>
                  Public grades {{ listing.schools.seniorHigh.grade_Range }}
                </p>
              </b-col>
              <b-col md="3" class="d-none d-md-block">
                <p>2 miles away</p>
              </b-col>
              <b-col md="3" sm="4" cols="4" class="text-right">
                <p>Rated {{ listing.schools.seniorHigh.rating }}/10</p>
              </b-col>
            </b-row>
            <b-row v-else>
              This district has no High school
              <b-col md="12" class="pl-0 pr-0">
                <hr />
              </b-col>
            </b-row>
          </b-tab>
        </b-tabs>
      </b-col>
    </b-row>
    <b-row>
      <b-col md="12" class="ratingResource">
        Ratings provided by
        <a href="http://www.greatschools.org" target="_blank">
          www.greatschools.org
        </a>
      </b-col>
    </b-row>
    <div class="row">
      <div class="col-md-12 pl-0 pr-0">
        <hr />
      </div>
    </div>
    <!--Tour insights-->
    <b-row class="kd theme-font-light tour-insights">
      <b-col cols="12">
        <p class="headers fw-600 mb-0">
          Tour insights <small>{{ listing.numberOfRatings }} tours</small>
        </p>
        <p class="font-12 color-base">
          All rating and feedback provided by Nexme who are verified home
          viewers.
        </p>
      </b-col>

      <b-col cols="12" class="mt-3 mb-4">
        <p class="font-14 fw-600 color-dark mb-1">
          Home rating
        </p>
        <div v-if="listing.rating !== 0" class="d-flex align-items-end">
          <span class="font-14">Overall</span>
          <span class="pl-2">
            <StarRating
              :rating="listing.rating"
              read-only
              :star-size="15"
              :show-rating="false"
            ></StarRating>
          </span>
          <span class="font-10 pl-1">
            /{{ listing.numberOfRatings }} ratings
          </span>
        </div>
        <div v-else class="font-14">
          No rating recorded yet.
        </div>
      </b-col>
      <b-col v-if="listing.sentiments.length !== 0" cols="12" class="pt-4">
        <p class="font-14 fw-600 color-dark">Positive feedback</p>
        <div class="row text-center">
          <div class="feedback-col">
            <img
              src="@/assets/feedback/positive/mountain_icon.svg"
              alt="mountain_icon"
            />
            <p class="font-10 color-base">Great views</p>
          </div>
          <div class="feedback-col">
            <img src="@/assets/feedback/positive/sun_icon.svg" alt="sun_icon" />
            <p class="font-10 color-base">Natural light</p>
          </div>
          <div class="feedback-col">
            <img
              src="@/assets/feedback/positive/floorplan_icon.svg"
              alt="floorplan_icon"
            />
            <p class="font-10 color-base">Nice floorplan</p>
          </div>
          <div class="feedback-col">
            <img
              src="@/assets/feedback/positive/walkable_icon.svg"
              alt="walkable_icon"
            />
            <p class="font-10 color-base">Walkable area</p>
          </div>
        </div>
      </b-col>
      <b-col v-if="listing.sentiments.length !== 0" cols="12">
        <p class="font-14 fw-600 color-dark">Negative feedback</p>
        <div class="row text-center">
          <div class="feedback-col">
            <img
              src="@/assets/feedback/negative/tools_icon.svg"
              alt="Needs work"
            />
            <p class="font-10 color-base">Needs work</p>
          </div>
          <div class="feedback-col">
            <img
              src="@/assets/feedback/negative/Car_icon.svg"
              alt="Limited parking"
            />
            <p class="font-10 color-base">Limited parking</p>
          </div>
          <div class="feedback-col">
            <img
              src="@/assets/feedback/negative/Loud_icon.svg"
              alt="Loud area"
            />
            <p class="font-10 color-base">Loud area</p>
          </div>
          <div class="feedback-col">
            <img
              src="@/assets/feedback/negative/Money_icon.svg"
              alt="High HOA"
            />
            <p class="font-10 color-base">High HOA</p>
          </div>
        </div>
      </b-col>
    </b-row>

    <!--Crime-->
    <b-row v-if="false" class="kd theme-font-light">
      <b-col cols="12">
        <p class="theme-font-semi-bold headers">Crime</p>
        <b-tabs content-class="mt-3" fill>
          <b-tab title="All" active>
            all
          </b-tab>
          <b-tab title="Assault">
            Assault
          </b-tab>
          <b-tab title="Theft">
            Theft
          </b-tab>
          <b-tab title="Vandalism">
            Vandalism
          </b-tab>
          <b-tab title="Burglary">
            Burglary
          </b-tab>
        </b-tabs>
      </b-col>
    </b-row>

    <b-row class="kd theme-font-light bg-base pt-2">
      <b-col cols="12">
        <p class="theme-font-semi-bold headers fw-600 font-13 mb-1">
          Seen enough?
        </p>
        <p class="font-12 fw-500 mb-4">
          Put together an offer and submit it right through the app. A
          <b>Nexme</b> agent partner will represent you and will form your offer
          into a legal contract, which you will sign to finalize. The
          <b>Nexme</b> agent partner commits to give back 50% commission fee to
          you.
        </p>
      </b-col>
      <div class="col-md-12 mb-3">
        <div class="d-flex justify-content-center">
          <button class="btn btn-green btn-block" @click="makeanoffer()">
            Make an offer
          </button>
          <!-- <div class="mao" @click="makeanoffer()">
            <span class="theme-font-semi-bold">Make an offer</span>
          </div> -->
        </div>
      </div>
      <!--Make an offer-->
    </b-row>

    <b-row class="kd theme-font-light">
      <b-col cols="12" class="mb-2 mt-3 font-12 color-dark fw-500">
        <img src="@/assets/images/nwmls.png" alt="" />
        NWMLS has provide this listing and all its details. the "Three Tree"
        icon refers to an NWMLS listing.
      </b-col>
    </b-row>
    <b-modal
      id="shareModal"
      ref="$shareModal"
      centered
      hide-footer
      title="Share"
    >
      <p class="my-4">
        <input
          ref="$shareLink"
          type="text"
          readonly
          class="form-control"
          :value="'https://nexme.com/home/' + listing.state + '/' + listing.mls"
        />
      </p>
      <template>
        <b-button size="sm" variant="primary" block @click="copyToClipboard()">
          Copy Share Link
        </b-button>
      </template>
    </b-modal>

    <b-modal
      id="jonLiveModal"
      ref="$jonLiveModal"
      centered
      hide-footer
      title="Join the live stream open house"
    >
      <p
        v-if="openHsDesc"
        class="font-12 fw-500 lh-16 mb-0"
        style="line-height:16px !important"
      >
        {{ openHsDesc }}
      </p>
      <p class="mb-4 mt-2">
        <input
          ref="$jonLiveLink"
          type="text"
          readonly
          class="form-control"
          :value="liveStreamUrl"
        />
      </p>
      <template>
        <a
          :href="liveStreamUrl"
          target="_blank"
          class="btn btn-green btn-block"
        >
          Join
        </a>
      </template>
    </b-modal>

    <b-modal
      id="scheduleTourModal"
      centered
      hide-footer
      title="Download the app!"
    >
      <p class="mb-4 mt-0 font-17 fw-400">
        {{ popUpMessage }}
      </p>
      <div class="d-flex justify-content-around">
        <a :href="appleUrl" target="_blank" class="col-sm-6">
          <img
            src="@/assets/icons/apple_logo.png"
            class="img-fluid"
            alt="App Store"
          />
        </a>
        <a :href="googleUrl" target="_blank" class="col-sm-6">
          <img
            src="@/assets/icons/google_logo.png"
            class="img-fluid"
            alt="Play Store"
          />
        </a>
      </div>
    </b-modal>

    <startLogin
      v-if="launchLogin"
      :start-login-box="launchLogin"
      @showLogin="unmountComp"
    ></startLogin>
  </b-row>
</template>

<script>
import StarRating from "vue-star-rating";
import axios from "axios";
import ImageButton from "./ImageButton";

import startLogin from "@/components/layout/login/loginModals";
import MapAgent from "@/components/MapAgent";
import infoData from "@/data/infoData.json";
import { Favorite } from "@/plugins/services/favorite";
import { extraHelpers } from "@/plugins/mixins/extra.js";
import { Helper } from "@/plugins/mixins/helper";
export default {
  name: "ListingDetailRight",
  components: {
    ImageButton,
    StarRating,
    startLogin,
    MapAgent
  },
  mixins: [Favorite, Helper],
  props: {
    listing: {
      type: Object,
      default: () => {
        return {};
      }
    },
    tourInsight: {
      type: Object,
      default: () => {
        return {};
      }
    },
    isFavorited: Boolean
  },
  data() {
    return {
      isAgent: false,
      showfullDetails: false,
      info: infoData.detailsModal,
      favorited: false,
      propertyInFavList: false,
      favoritedList: [],
      hideContent: false,
      showMortgageForm: false,
      max: 100,
      crStatus: {},
      agentCount: 15,
      liveStreamUrl: "",
      openHsDesc: null,
      appPhone: "",
      markers: [
        {
          position: {
            lat: this.listing.latitude,
            lng: this.listing.longitude
          }
        }
      ],
      appRequestSent: false,
      showAppRequest: false,
      launchLogin: false,
      creditScoreText: "700+ Credit score",
      principalInterestMonthly: 0,
      principalAmount: 0,
      homePriceValue: 0,
      downAmountValue: 0,
      downPercent: 0,
      monthlyPayment: 0,
      propertyTax: 0,
      propertyTaxesPercent: 0,
      monthlyTax: 0,
      propertyTaxAmount: 0,
      hoaDues: 0,
      homeInsurancePercent: 0.24,
      homeInsurance: 0,
      interestRate: 0,
      interestPercent: 4.019,
      totalInterest: 0,
      mortgageYears: 30,
      creditScore: 700,
      paymentPerMonth: 0,
      tempHomeIncPerc: 0,
      popUpMessage: "",
      mortgageInsurance: 0,
      tempMortgageIncPerc: 0,
      tempHomIncVal: 0,
      mortgageInsurancePercentage: 1.32,
      propertyHistory: [],
      tempMortgageInc: 0,
      tempProTaxVal: 0,
      tempHomePrice: 0,
      downPaymentTempVal: 0,
      listingDescription: "",
      showReadMore: false,
      readMoreExpend: false,
      showMoreText: " Read More",
      progressBarVals: {
        hoa: 0,
        loan: 0,
        insurance: 0,
        pTax: 0,
        mortgageInsurance: 1
      },
      mortgageYearsOptions: [
        { value: 30, text: "30-Year Fixed" },
        { value: 15, text: "15-Year Fixed" },
        { value: 7, text: "7/1 ARM" },
        { value: 5, text: "5/1 ARM" }
      ],
      mortgageText: "30-year fixed",
      creditScoreOptions: [
        { value: 540, text: "540+ Credit score" },
        { value: 560, text: "560+ Credit score" },
        { value: 580, text: "580+ Credit score" },
        { value: 600, text: "600+ Credit score" },
        { value: 700, text: "700+ Credit score" },
        { value: 800, text: "800+ Credit score" },
        { value: 820, text: "820+ Credit score" },
        { value: 840, text: "840+ Credit score" }
      ],
      Status: {
        A: "Active",
        CT: "Contingent",
        INC: "Incomplete",
        PB: "Pending Back-up Requested",
        PF: "Pending Feasibility",
        PI: "Pending Inspection",
        PS: "Pending Short Sale",
        P: "Pending",
        SFR: "Sale Fail Release",
        CA: "Cancelled",
        T: "Temporarily Off-Marker",
        E: "Expired",
        S: "Sold",
        R: "Rented",
        "S-UL": "Unlisted Sales"
      },
      interestInfo:
        "This calculation is for education purpose only. The should not be used as your sole source of information. The results of these calculations are not a loan offer or solicitation, nor is it financial or legal advice. Please consult with one of our Nexme lender partners for more information.",
      agentCommission:
        "This is the amount the seller is offering to a real estate agent who represents the buyer. The buyer's agent commission is often a percentage of the final sale price.",
      cashBackInfo:
        "Traditionally, real estate agents charge up to a 3% buyer’s agent commission. This is the amount the seller is offering to a buyer’s agent. Our local Nexme agent partners commit to a 50% reduction in commission and we pass on the remaining 50% back to you. Nexme does not take a commission fee.",
      secheduleMsg:
        "Schedule Tour is coming soon for web! Feel free to Schedule Tour through our mobile app and get started on scheduling your home tour right away.",
      tuorNowMgs:
        "Tour Now is coming soon for web! Feel free to Tour Now through our mobile app and get started on touring a home right away.",
      makeAnOfferMsg:
        "Make an Offer is coming soon for web! Feel free to Make an offer through our mobile app and get started right away.",
      sellAHomeMsg:
        "Sell a home is coming soon for web! Feel free to Sell a home through our mobile app and get started right away.",
      loanType:
        "As an example a 30-Year Fixed rate loan has a set interest rate for the duration of the loan. In contrast, an adjustable-rate mortgage (ARM) such as a 5/1 ARM, usually has a lower set interest rate for the first 5-Years and then changes to an adjustable interest rate the following 25-Years. "
    };
  },

  computed: {
    hoaDs() {
      return parseInt(this.hoaDues);
    },
    appleUrl() {
      return this.$store.getters.getAppleUrl;
    },
    googleUrl() {
      return this.$store.getters.getGoogleUrl;
    },
    downAmount: {
      get() {
        return this.addComma((this.principalAmount * this.downPercent) / 100);
      },
      set(newValue) {
        return parseInt(
          (this.principalAmount = (parseInt(newValue) * 100) / this.downPercent)
        );
      }
    },
    getPrincipalAndInterestPerMonth: {
      get() {
        const loanAmount =
          (this.principalAmount * (100 - this.downPercent)) / 100;
        return this.PMT(
          this.interestRate / 100 / 12,
          this.mortgageYears * 12,
          -loanAmount,
          0.0,
          false
        );
      },
      set(newName) {
        return parseInt(newName);
      }
    },
    getPropertyTaxesPercent: {
      get() {
        return (this.homePriceValue / this.propertyTaxAmount).toFixed(0);
      },
      set(val) {
        this.propertyTaxesPercent = val;
      }
    },
    mortgageInsurancePercent() {
      const loanAmount =
        (this.principalAmount * (100 - this.downPercent)) / 100;
      return (this.pricePerMonth / loanAmount) * 100 * 12;
    },
    sortHistory: {
      get() {
        const history = this.listing.listingHistory;
        return history.sort(function(a, b) {
          const aa = new Date(a.changeDate);
          const bb = new Date(b.changeDate);
          return bb.getTime() - aa.getTime();
        });
      },
      set(history) {
        return history;
      }
    },
    getAgentCount() {
      return this.$store.getters.getActiveAgentCount;
    },
    getTimeDistance() {
      return this.$store.getters.getAgentTimeDistance;
    }
  },
  watch: {
    hoaDues() {
      this.barPercentCalc();
    },
    homeInsurancePercent() {
      this.barPercentCalc();
    }
  },
  created() {
    this.getUserData();
    const listing = this.listing;
    const actualChashDwnPercent = listing.listingPrice > 1000000 ? 20 : 10;
    // const intRte =
    //   localStorage.getItem("InterestRate") !== null
    //     ? Number(localStorage.getItem("InterestRate"))
    //     : 22;
    if (listing.marketingRemarks.length < 250) {
      this.showReadMore = false;
    } else {
      this.showReadMore = true;
      this.readMoreExpend = false;
      this.listingDescription = this.shorten(listing.marketingRemarks, 245);
    }
    const intRte = localStorage.getItem("InterestRate");
    // const homIns = localStorage.getItem("HomeInsurance");
    this.downPercent = Number(localStorage.getItem("DPPercentage"))
      ? Number(localStorage.getItem("DPPercentage"))
      : actualChashDwnPercent;
    this.homePriceValue = this.addComma(listing.listingPrice);
    const dpa =
      (this.removeComma(this.homePriceValue) * this.downPercent) / 100;
    this.principalAmount = this.removeComma(this.homePriceValue) - dpa;
    // listing.hasOwnProperty("interestPercent")
    const finalIntRte = intRte || 3.625;
    this.interestRate = Object.prototype.hasOwnProperty.call(
      listing,
      "interestPercent"
    )
      ? listing.interestPercent
      : finalIntRte;
    // listing.hasOwnProperty("year")
    this.mortgageYears = Object.prototype.hasOwnProperty.call(listing, "year")
      ? listing.year
      : 30;
    let txPercent = 0.76;
    if (listing.taxAmount !== 0) {
      txPercent = (listing.taxAmount / listing.listingPrice) * 100;
    }

    this.propertyTaxesPercent = txPercent;
    this.propertyTaxAmount = this.addComma(listing.taxAmount);
    if (listing.taxAmount) {
      this.monthlyTax = (listing.taxAmount / 12).toFixed(2);
    } else {
      const txAm = (listing.listingPrice * 0.0076).toFixed(2);
      this.monthlyTax = this.addComma((txAm / 12).toFixed(2));
      this.propertyTaxAmount = this.addComma(txAm);
    }
    this.hoaDues = listing.hoa;
    // listing.hasOwnProperty("creditScore")
    this.creditScore = Object.prototype.hasOwnProperty.call(
      listing,
      "creditScore"
    )
      ? listing.creditScore
      : 840;
    if (this.removeComma(this.homePriceValue) < 625000) {
      this.mortgageInsurancePercentage = 0.85;
    } else {
      this.mortgageInsurancePercentage = 0.85; // 1.25
    }
    this.downAmountValue = this.calcDownAmount();
    // Home insurance
    const homeIns = localStorage.getItem("HomeInsurance");
    if (homeIns !== null) {
      this.homeInsurancePercent = homeIns;
    }

    const hip = this.removeComma(this.homeInsurancePercent);
    const ppa = this.removeComma(this.principalAmount);

    this.homeInsurance = parseInt((ppa * hip) / 100 / 12);

    this.mortgageInsurance = this.calcMortgageInsurance();
    this.barPercentCalc();
    const $this = this;
    setTimeout(() => {
      $this.favorited = $this.isFavorited;
    }, 500);
  },
  mounted() {
    this.principalInterestMonthly = this.getPrincipalAndInterestPerMonth;
    this.downAmountValue = this.calcDownAmount();

    if (this.listing.status) {
      this.crStatus = this.statusMaker(
        this.$store.getters.getFullPropertyStatus,
        this.listing.status
      );
    }
  },
  methods: {
    liveStreamJoin(op) {
      this.$refs.$jonLiveModal.show();
      this.liveStreamUrl = op.url;
      this.openHsDesc = op.description;
    },
    hasLiveStream() {
      return (
        this.listing.openHouses[0] && this.listing.openHouses[0].type === "VIR"
      );
    },
    homePriceFocused($event) {
      this.tempHomePrice = $event.target.value;
    },
    showReadMoreBtn() {
      const listing = this.listing;
      if (this.readMoreExpend) {
        this.readMoreExpend = false;
        this.listingDescription = this.shorten(listing.marketingRemarks, 245);
        this.showMoreText = " Read More";
      } else {
        this.readMoreExpend = true;
        this.listingDescription = listing.marketingRemarks;
        this.showMoreText = " Read Less";
      }
      // if (listing.marketingRemarks.length === 250) {
      //   this.showReadMore = false;
      //   this.listingDescription = listing.marketingRemarks;
      // } else {
      //   this.showReadMore = true;
      //   this.listingDescription = this.shorten(listing.marketingRemarks, 245);
      // }
    },
    changeHomePriceAmount($event) {
      const val = $event.target.value;

      if (this.tempHomePrice !== val) {
        const dpa = (val * this.downPercent) / 100;
        this.principalAmount = val - dpa;
        const loanAmount =
          (this.principalAmount * (100 - this.downPercent)) / 100;
        const paipm = this.PMT(
          this.interestRate / 100 / 12,
          this.mortgageYears * 12,
          -loanAmount,
          0.0,
          false
        );

        this.principalInterestMonthly = paipm;
        this.homePriceValue = val;
        this.barPercentCalc();
      }
    },
    calculateMortgage() {
      // console.log(this.annualTax,this.annualInsurance);
      // let principleAndInterest =
      //     this.interestPercent /
      //     100 /
      //     12 *
      //     this.principalAmount /
      //     (1 - Math.pow(1 + this.interestRate / 100 / 12, -this.mortgageYears * 12));

      // let tax = this.getPropertyTaxesPerMonth;
      // let insurance = this.homeInsurance;
      // let totalMonthlyPayment =
      //   this.getPrincipalAndInterestPerMonth + tax + insurance;

      this.showMortgageForm = false;
      // principleAndInterest = Math.round(principleAndInterest * 100) / 100;
      // tax = Math.round(tax * 100) / 100;
      // insurance = Math.round(insurance * 100) / 100;
      // totalMonthlyPayment = Math.round(totalMonthlyPayment * 100) / 100;
      // this.paymentPerMonth = totalMonthlyPayment;
    },
    resetCalculator() {
      const listing = this.listing;
      // const $this = this;
      localStorage.removeItem("DPPercentage");
      localStorage.removeItem("InterestRate");
      localStorage.removeItem("HomeInsurance");

      this.downPercent = listing.listingPrice > 1000000 ? 20 : 10;
      this.homePriceValue = this.addComma(listing.listingPrice);
      this.downAmountValue = this.calcDownAmount();

      const loan =
        this.removeComma(this.homePriceValue) -
        this.removeComma(this.downAmountValue);
      // reset property tax
      if (listing.taxAmount) {
        this.monthlyTax = this.addComma((listing.taxAmount / 12).toFixed(2));
        const numDivide =
          listing.taxAmount / this.removeComma(listing.listingPrice);
        this.propertyTaxesPercent = numDivide * 100;
        this.propertyTaxAmount = this.addComma(listing.taxAmount);
      } else {
        const txAm = (listing.listingPrice * 0.0076).toFixed(2);
        this.monthlyTax = this.addComma((txAm / 12).toFixed(2));
        this.propertyTaxAmount = this.addComma(txAm);
        this.propertyTaxesPercent = 0.76;
      }

      this.hoaDues = listing.hoa;

      this.homeInsurancePercent = 0.24;
      this.homeInsurance = parseInt(
        (this.principalAmount * this.homeInsurancePercent) / 100 / 12
      ).toFixed(2);

      this.interestRate = Object.prototype.hasOwnProperty.call(
        listing,
        "interestPercent"
      )
        ? listing.interestPercent
        : 3.625;
      // mortgage and mortgage insurance
      this.mortgageInsurancePercentage = 0.85;

      const PercA = this.mortgageInsurancePercentage / 100;
      const PercB = PercA * this.removeComma(loan);
      const PercD = PercB / 12;
      this.mortgageInsurance = PercD.toFixed(2);
      const paippm = this.PMT(
        this.interestRate / 100 / 12,
        this.mortgageYears * 12,
        -loan,
        0.0,
        false
      );
      this.principalInterestMonthly = paippm;

      this.mortgageText = "30-year fixed";
      // setTimeout(() => {
      //   listing.taxAmount = $this.addComma(listing.taxAmount);
      // }, 100);
      this.barPercentCalc();
    },
    copyToClipboard() {
      this.$refs.$shareLink.focus();
      this.$refs.$shareLink.select();
      document.execCommand("copy");
      this.$refs.$shareModal.hide();
    },
    calcMortgage() {
      const P =
        this.removeComma(this.homePriceValue) -
        this.removeComma(this.downAmountValue);
      const i = this.interestRate / 100 / 12;
      const n = this.mortgageYears * 12;
      const x = (1 + i) ** n;
      const M = (P * ((i * x) / (x - 1))).toFixed(2);
      return M;
    },
    toggleForm() {
      this.showMortgageForm = !this.showMortgageForm;
    },
    mortgageFormSubmit() {
      console.log(this);
    },
    numberWithCommas(x) {
      return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },
    scheduleclicked() {
      this.popUpMessage = this.secheduleMsg;
      this.$bvModal.show("scheduleTourModal");
      // this.showAppRequest = true;
      // localStorage.setItem("selectedListing", JSON.stringify(this.listing));
      // this.$router.push("schedule-tour");
    },
    tournowclicked() {
      this.popUpMessage = this.tuorNowMgs;
      this.$bvModal.show("scheduleTourModal");
      // this.showAppRequest = true;
      // localStorage.setItem("selectedListing", JSON.stringify(this.listing));
      // this.$router.push("confirm-tour");
    },
    makeanoffer() {
      this.popUpMessage = this.makeAnOfferMsg;
      // this.$router.push("listings/make-offer");
      this.$bvModal.show("scheduleTourModal");
    },
    isEmpty(obj) {
      for (const key in obj) {
        // obj.hasOwnProperty(key)
        if (Object.prototype.hasOwnProperty.call(obj, key)) return false;
      }
      return true;
    },
    PMT(ir, np, pv, fv, type) {
      /*
       * ir   - interest rate per month
       * np   - number of periods (months)
       * pv   - present value
       * fv   - future value
       * type - when the payments are due:
       *        0: end of the period, e.g. end of month (default)
       *        1: beginning of period
       */
      let pmt;

      fv || (fv = 0);
      type || (type = 0);

      if (ir === 0) return -(pv + fv) / np;

      const pvif = (1 + ir) ** np;
      pmt = (-ir * pv * (pvif + fv)) / (pvif - 1);

      if (type === 1) pmt /= 1 + ir;

      return pmt;
    },
    openHours(open, close) {
      return extraHelpers.getOpenHours(open, close);
    },
    likeToggle(mls) {
      const $this = this;
      const getAuth = localStorage.getItem("userToken");
      if (getAuth !== null) {
        if (this.favorited === false) {
          const add = this.addToFavorite(mls);
          add
            .then(function() {
              $this.favorited = true;
            })
            .catch(function(err) {
              if (err.message === "Request failed with status code 401") {
                // $this.launchLogin = true;
                $this.favorited = false;
              }
            });
        } else {
          this.favorited = false;
          this.removeFromFavorite(mls);
        }
        return null;
      } else {
        $this.launchLogin = true;
        $this.favorited = false;
      }
    },
    like(mls) {
      const This = this;
      const getAuth = localStorage.getItem("userToken");
      const Url = this.$apiPath("main", "Favorites/Add/", mls);
      this.$favChannel("New listening connected!");

      if (getAuth !== null) {
        axios({
          method: "POST",
          url: Url,
          headers: {
            "Content-Type": "application/json",
            "X-Client-Token": This.$store.state.X_CLIENT_TOKEN,
            "X-User-Token": This.$store.state.X_USER_TOKEN
          },
          data: {
            listing_mls: mls
          }
        }).then(
          (result) => {
            if (result.data.statusCode === "Success") {
              This.favorited = true;
              const favData = This.listing;
              const locGeometry = {
                location: {
                  lat: favData.latitude,
                  lng: favData.longitude
                }
              };
              This.$store.commit("setGeometry", locGeometry);
              localStorage.setItem("favGeometry", JSON.stringify(locGeometry));
              This.propertyInFavList = true;
              This.$store.commit("setAddfavoriteList", `${mls}`);
              const allNewFvs = This.$store.getters.getfavoriteList;
              console.log(allNewFvs);
              localStorage.setItem("favoriteList", JSON.stringify(allNewFvs));
            }
          },
          (error) => {
            console.log(new Error(error));
          }
        );
      } else {
        This.launchLogin = true;
        This.favorited = false;
      }
    },
    disLike(mls) {
      const This = this;
      const getAuth = localStorage.getItem("userToken");
      const Url = this.$apiPath("main", "Favorites/Remove/", mls);
      if (getAuth !== null) {
        axios({
          method: "POST",
          url: Url,
          headers: {
            "Content-Type": "application/json",
            "X-Client-Token": This.$store.state.X_CLIENT_TOKEN,
            "X-User-Token": This.$store.state.X_USER_TOKEN
          },
          data: {
            listing_mls: mls
          }
        }).then(
          (result) => {
            if (result.data.statusCode === "Success") {
              This.favorited = false;
              This.$store.commit("setRemoveFavoriteList", `${mls}`);
              const allNewFvs = This.$store.getters.getfavoriteList;
              console.log(allNewFvs);
              localStorage.setItem("favoriteList", JSON.stringify(allNewFvs));
            }
          },
          (error) => {
            console.log(new Error(error));
          }
        );
      }
    },
    setPropertyType(typeVal) {
      const arrObj = [
        { type: "RESI", val: "Residential" },
        { type: "COND", val: "Condominium" },
        { type: "MANU", val: "Manufactured" },
        { type: "MULT", val: "Multi-Family" },
        { type: "VACL", val: "Vacant Land" },
        { type: "Farm", val: "Farm & Ranch" }
      ];

      let type = "";
      for (let i = 0; i <= arrObj.length; i++) {
        if (arrObj[i] !== undefined) {
          if (arrObj[i].type === typeVal) {
            type = arrObj[i].val;
          }
        }
      }

      return type;
    },
    mortgageChange(Nm, text) {
      this.mortgageYears = Nm;
      this.mortgageText = text;
    },
    creditScoreChange(value, text) {
      this.creditScoreText = text;
      this.creditScore = value;
    },
    priceChange(oldPrice, newPrice) {
      if (oldPrice > newPrice) {
        return newPrice - oldPrice;
      } else {
        return oldPrice - newPrice;
      }
    },
    submitAppRequest() {
      this.appRequestSent = true;
      const This = this;

      setTimeout(function() {
        This.showAppRequest = false;
        This.appRequestSent = false;
        This.appPhone = "";
      }, 1500);
    },
    connectWithLender(data) {
      localStorage.setItem("sendToLender", JSON.stringify(data));
      this.$router.push({ name: "mortgage" });
    },
    unmountComp() {
      this.launchLogin = false;
    },
    propStatus(data) {
      for (const key of Object.keys(this.Status)) {
        if (key === data) {
          return this.Status[key];
        } else {
          return data.split(",").join(", ");
        }
      }
    },
    statusValue(data) {
      if (this.Status[data] !== undefined) {
        return this.Status[data];
      }
    },
    calcMortgageInsurance(val) {
      const loan =
        this.removeComma(this.homePriceValue) -
        this.removeComma(this.downAmountValue);
      if (val) {
        // return (((val / 100) * this.removeComma(loan)) / 12).toFixed(2);
        const PercA = val / 100;
        const PercB = PercA * this.removeComma(loan);
        const PercD = PercB / 12;
        return PercD.toFixed(2);
      } else {
        const PercA = this.mortgageInsurancePercentage / 100;
        const PercB = PercA * this.removeComma(loan);
        const PercD = PercB / 12;
        return PercD.toFixed(2);
      }
    },
    inputFocusOn($event, name = null) {
      if (name === "PropertyTaxes") {
        this.prpTaxFocused($event);
      }
      if (name === "homePrice") {
        this.homePriceFocused($event);
      }
      if (name === "downPayment") {
        this.downPaymentFocused($event);
      }
      $event.target.value = this.removeComma($event.target.value);
    },
    downPaymentFocused($event) {
      this.downPaymentTempVal = this.removeComma($event.target.value);
    },
    inputFocusOut($event, type) {
      const $this = this;
      const pVal = this.removeComma($event.target.value);
      if (type === "downAmountValue") {
        if (this.downPaymentTempVal === pVal) {
          this.downPercent = (
            (pVal * 100) /
            this.removeComma(this.homePriceValue)
          ).toFixed(0);
        }
      }

      if (type === "homePriceValue") {
        this.homePriceValue = pVal;
        this.downAmountValue = this.calcDownAmount();
        // console.log(this.downAmountValue);
      }

      setTimeout(() => {
        $event.target.value = $this.addComma(pVal);
      }, 100);
    },
    calcDownAmount() {
      this.barPercentCalc();
      return this.addComma(
        (
          (this.downPercent / 100) *
          this.removeComma(this.homePriceValue)
        ).toFixed(2)
      );
    },
    changeDPPercentage() {
      const ppA = this.removeComma(this.principalAmount);
      const loanAmount = (ppA * (100 - this.downPercent)) / 100;
      const paipm = this.PMT(
        this.interestRate / 100 / 12,
        this.mortgageYears * 12,
        -loanAmount,
        0.0,
        false
      );

      this.principalInterestMonthly = paipm;
      this.downAmountValue = this.calcDownAmount();
      localStorage.setItem("DPPercentage", this.downPercent);
    },
    prpTaxPecFocused($event) {
      this.tempProTaxPercVal = this.removeComma($event.target.value);
    },
    changePropertyTaxesPercent($event) {
      const pVal = this.removeComma($event.target.value);
      if (this.tempProTaxPercVal !== pVal) {
        this.propertyTaxesPercent = pVal;
        const pta = (
          (pVal / 100) *
          this.removeComma(this.homePriceValue)
        ).toFixed(0);
        this.propertyTaxAmount = this.addComma(pta);
        const mtx = (this.removeComma(this.propertyTaxAmount) / 12).toFixed(2);
        this.monthlyTax = this.addComma(mtx);
        this.barPercentCalc();
      }
    },
    prpTaxFocused($event) {
      this.tempProTaxVal = this.removeComma($event.target.value);
    },
    changePropertyTaxes($event) {
      const val = this.removeComma($event.target.value);
      if (this.tempProTaxVal !== val) {
        const $this = this;

        const PercA =
          this.removeComma(val) / this.removeComma(this.homePriceValue);
        const PercB = PercA * 100;
        this.propertyTaxesPercent = PercB;
        this.monthlyTax = this.addComma((val / 12).toFixed(2));

        this.barPercentCalc();
        setTimeout(() => {
          $event.target.value = $this.addComma(val);
        }, 100);
      }
    },
    barPercentCalc() {
      const loan = Number(this.getPrincipalAndInterestPerMonth.toFixed(2));
      const pTax = Number(this.removeComma(this.monthlyTax));
      const insurance = Number(this.removeComma(this.homeInsurance));
      const hoa = Number(this.removeComma(this.hoaDs));
      const mortgageInsurance = Number(
        this.removeComma(this.mortgageInsurance)
      );
      let totalPayment = 0;
      if (this.downPercent < 20) {
        totalPayment = loan + pTax + insurance + hoa + mortgageInsurance;
      } else {
        totalPayment = loan + pTax + insurance + hoa;
      }

      this.monthlyPayment = totalPayment.toFixed(2);

      this.progressBarVals.loan = this.percentOfNumber(loan, totalPayment);
      this.progressBarVals.pTax = this.percentOfNumber(pTax, totalPayment);
      this.progressBarVals.insurance = this.percentOfNumber(
        insurance,
        totalPayment
      );

      this.progressBarVals.hoa = this.percentOfNumber(hoa, totalPayment);
      this.progressBarVals.mortgageInsurance = this.percentOfNumber(
        mortgageInsurance,
        totalPayment
      );
    },
    homeIncFocused($event) {
      this.tempHomIncVal = this.removeComma($event.target.value);
    },
    homeInsuranceChange($event) {
      const val = this.removeComma($event.target.value);
      if (this.tempHomIncVal !== val) {
        this.homeInsurance = this.removeComma(val);
        const loan =
          this.removeComma(this.homePriceValue) -
          this.removeComma(this.downAmountValue);
        const PercA = (val * 12) / loan;
        const PercB = PercA * 100;
        this.homeInsurancePercent = PercB.toFixed(6);
        this.barPercentCalc();
      }
    },
    hipFocused($event) {
      this.tempHomeIncPerc = this.removeComma($event.target.value);
    },
    homeInsurancePercentChange($event) {
      const pVal = this.removeComma($event.target.value);
      if (this.tempHomeIncPerc !== pVal) {
        localStorage.setItem("HomeInsurance", pVal);
        const val =
          ((this.removeComma(this.homePriceValue) -
            this.removeComma(this.downAmountValue)) *
            pVal) /
          100 /
          12;
        this.homeInsurance = val.toFixed(2);

        this.barPercentCalc();
      }
    },
    mortgageInsurancePercentChange($event) {
      const val = this.removeComma($event.target.value);
      if (this.tempMortgageIncPerc !== val) {
        this.mortgageInsurance = this.calcMortgageInsurance(val);
        this.barPercentCalc();
      }
    },
    updateInterestRate($event) {
      const intRt = this.removeComma($event.target.value);
      localStorage.setItem("InterestRate", intRt);
      // const loan =
      //   this.removeComma(this.homePriceValue) -
      //   this.removeComma(this.downAmountValue);
      // const intPerc = (intRt / 100) * loan;
      // let intRteMonth = 0;
      // if (this.mortgageYears > 15) {
      //   intRteMonth = this.mortgageYears * 12;
      // }
      // const clcIntRte = (intPerc.toFixed(2) / intRteMonth).toFixed(2);

      const loanAmount =
        (this.principalAmount * (100 - this.downPercent)) / 100;
      const mntInt = this.PMT(
        this.interestRate / 100 / 12,
        this.mortgageYears * 12,
        -loanAmount,
        0.0,
        false
      );
      this.principalInterestMonthly = mntInt;
      this.barPercentCalc();
    },
    mortgageInsuranceFocused($event) {
      this.tempMortgageInc = this.removeComma($event.target.value);
    },
    mipFocused($event) {
      this.tempMortgageIncPerc = this.removeComma($event.target.value);
    },
    mortgageInsuranceChange($event) {
      const mntPrice = this.removeComma($event.target.value);
      if (this.tempMortgageInc !== mntPrice) {
        const loan =
          this.removeComma(this.homePriceValue) -
          this.removeComma(this.downAmountValue);
        const PercA = (mntPrice * 12) / loan;
        const PercB = PercA * 100;
        this.mortgageInsurancePercentage = PercB.toFixed(6);
        this.barPercentCalc();
      }
    },
    openHouseTime(time) {
      const open = new Date(time.open);
      const close = new Date(time.close);
      const finalTime =
        this.formatAMPM(open, true) + " - " + this.formatAMPM(close, true);
      return finalTime;
    },
    formatDate(date) {
      const d = new Date(date);
      let month = "" + (d.getMonth() + 1);
      let day = "" + d.getDate();
      const year = d.getFullYear();

      if (month.length < 2) month = "0" + month;
      if (day.length < 2) day = "0" + day;

      return [month, day, year].join("/");
    },
    formatPhone(str) {
      const cleaned = ("" + str).replace(/\D/g, "");
      const match = cleaned.match(/^(\d{3})(\d{3})(\d{4})$/);

      if (match) {
        return match[1] + "- " + match[2] + "-" + match[3];
      }

      return null;
    },
    getUserData() {
      const $this = this;
      const Url = this.$apiPath("main", "Account/Details", "");
      axios({
        method: "GET",
        url: Url,
        headers: {
          "Content-Type": "application/json",
          "X-Client-Token": $this.$store.state.X_CLIENT_TOKEN,
          "X-User-Token": $this.$store.state.X_USER_TOKEN
        }
      }).then(
        (result) => {
          //   This.search = null;
          if (result.data.statusCode === "Success") {
            if (result.data.data.isAgent === true) {
              console.info("is agent true");
              $this.isAgent = true;
            } else {
              console.info("is agent false");
              $this.isAgent = false;
            }
          }
        },
        (error) => {
          throw new Error(error);
        }
      );
    }
  }
};
</script>
<style scoped>
/* .row.kd.theme-font-light.mp-r-5.mp-l-5 div {
  float: left;
} */
.tabs {
  box-shadow: 0px 0px 0px rgba(0, 0, 0, 0.16078);
  margin-bottom: 0px;
  padding: 0px 0px;
}
</style>
