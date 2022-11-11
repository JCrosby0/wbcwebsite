<template>
  <main>
    <div
      class="hero"
      :style="{
        '--url': `url('${heroImage}')`,
      }"
    ></div>
    <div class="welcome | container section">
      <div class="heading">
        <h1>
          Welcome to the home of the <br /><span class="wbc">WBC Wildcats</span>
        </h1>
      </div>
      <div class="body">
        <p class="text-lg">
          The Willetton Baseball Club (Inc) is a multi-diamond sports club,
          comprising <b>Tee-Ball</b>, <b>Baseball</b> and <b>Softball</b>. Known
          as the “<span class="wbc">WBC Wildcats</span>", we are one of the
          state's largest diamond sports clubs, catering from ages 4 and beyond.
          Founded in 1977, the club prides itself on providing a family friendly
          environment with a huge focus on junior development, as well as
          catering for the social or the serious diamond sport player of any
          age.
        </p>
        <NuxtLink to="/info"
          ><button
            class="border border-green-900 px-4 py-2 mt-4 rounded hover:bg-green-100"
          >
            Find out more...
          </button>
        </NuxtLink>
      </div>
    </div>
  </main>
</template>

<script lang="ts">
import Vue from 'vue'

const heroImages = {
  baseball: {
    src: '~/assets/img/wbc1819Premiers.jpg',
    img: require('~/assets/img/wbc1819Premiers.jpg'),
  },
  softball: {
    src: '~/assets/img/wscTeamPhoto.jpg',
    img: require('~/assets/img/wscTeamPhoto.jpg'),
  },
  teeball: {
    src: '~/assets/img/wtcStates.jpg',
    img: require('~/assets/img/wtcStates.jpg'),
  },
}
type layout = {
  selectedDivision: string
}
export default Vue.extend({
  name: 'IndexPage',
  computed: {
    heroImage() {
      if (!this || !this.$parent || !this.$parent.$parent) return
      const layout = this.$parent.$parent as unknown as layout
      const selection = layout.selectedDivision
      const key = (selection as keyof typeof heroImages) || 'baseball'
      // const key = (this.selection as keyof typeof heroImages) || 'baseball'
      return heroImages[key].img
    },
  },
})
</script>

<style scoped>
.container {
  width: 800px;
  margin: auto;
  /* background-color: #ddd; */
  /* min-height: 100%; */
  padding: 0 2em;
}
.hero {
  --header-height: calc(60px + 1em);
  --fold-height: 20vh;
  height: calc(100vh - var(--header-height) - var(--fold-height));
  width: 100%;
  background-image: url(assets/img/wtcStates.jpg);
  background-image: var(--url);
  background-size: cover;
  background-position: center;
}
h1 {
  font-size: xx-large;
  text-align: center;
}
.section {
  min-height: clamp(600px, 50vh, 1000px);
}
.welcome {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  height: 100%;
}
.welcome > .heading {
  flex: 0 0 20vh;
  margin: auto;
  display: flex;
  justify-content: center;
  align-items: center;
}
.welcome > .body {
  flex: 1 1 auto;
}
.wbc {
  color: var(--bottle-green);
  font-weight: bold;
}
</style>
