<template>
  <div class="layout-main">
    <!-- <div id="fb-root"></div>
    <script
      async
      defer
      crossorigin="anonymous"
      src="https://connect.facebook.net/en_US/sdk.js#xfbml=1&version=v15.0"
      nonce="8BaZPg1V"
    ></script> -->
    <TopNavVue
      class="layout-header"
      :selection="selectedDivision"
      @showSelection="handleShowSelection"
    ></TopNavVue>
    <main class="layout-body">
      <Nuxt />
    </main>
    <FooterVue class="layout-footer" :selection="selectedDivision"></FooterVue>
    <SelectDivisionVue
      v-show="showSelectDivision"
      class="layout-modal"
      :selection="selectedDivision"
      @selection="handleSelection"
    ></SelectDivisionVue>
  </div>
</template>
<script lang="ts">
import Vue from 'vue'
import TopNavVue from '@/components/TopNav.vue'
import FooterVue from '@/components/Footer.vue'
import SelectDivisionVue from '@/components/SelectDivision.vue'
export default Vue.extend({
  name: 'LayoutDefault',
  components: {
    TopNavVue,
    FooterVue,
    SelectDivisionVue,
  },
  data() {
    return {
      showSelectDivision: false,
      selectedDivision: 'baseball',
    }
  },
  methods: {
    handleSelection(payload: string): void {
      this.selectedDivision = payload
      this.showSelectDivision = false
    },
    handleShowSelection(): void {
      this.showSelectDivision = true
    },
  },
})
</script>

<style>
:root {
  --bottle-green: hsl(150, 77%, 14%);
  --gold: rgb(255, 213, 5);
  --green-100: hsl(90, 50%, 85%);
  --header-height: 76px;
  --footer-height: 191px;
  font-family: 'Open Sans', 'Gill Sans', 'Gill Sans MT', Calibri, sans-serif;
}
body,
.layout-main {
  min-height: 100vh;
  position: relative;
}
.layout-main > * {
  width: 100%;
  z-index: 0;
  position: relative;
}
.layout-header,
.layout-footer {
  z-index: 1;
}
.layout-modal {
  z-index: 2;
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
}
.layout-body {
  min-height: calc(100vh - var(--header-height) - var(--footer-height));
}

.layout-header {
  position: sticky;
  top: 0;
}
</style>
