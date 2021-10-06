<template>
  <div class="phoneNumberCheck inpC vue-tel-input">
    <div tabindex="0" class="vti__dropdown">
      <span class="vti__selection"
        ><div class="vti__flag us"></div>
        <!---->
        <span class="vti__dropdown-arrow">▼</span></span
      >
      <ul class="vti__dropdown-list" style="display: none;">
        <li class="vti__dropdown-item">
          <div class="vti__flag us"></div>
          <strong>United States</strong>
          <span>
            +1
          </span>
        </li>
        <li class="vti__dropdown-item highlighted">
          <div class="vti__flag ca"></div>
          <strong>Canada</strong>
          <span>
            +1
          </span>
        </li>
        <li class="vti__dropdown-item">
          <div class="vti__flag mx"></div>
          <strong>Mexico (México)</strong>
          <span>
            +52
          </span>
        </li>
      </ul>
    </div>
    <input
      id=""
      type="tel"
      placeholder="Enter your mobile number"
      autocomplete="off"
      name="phone"
      maxlength="11"
      tabindex="0"
      class="vti__input phoneNumberLg"
    />
  </div>
</template>

<script>
export default {
  name: "PhoneInput"
};
</script>
