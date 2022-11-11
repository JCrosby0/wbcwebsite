<template>
  <div class="navbar" :style="{ '--topOfPage': topOfPage }">
    <!-- <a href="#"> -->
    <div id="roundel-container">
      <NuxtLink to="/">
        <img
          src="~/assets/img/willetton_bc_roundel.png"
          :class="{
            roundel: true,
            'roundel-large': topOfPage && wideScreen,
          }"
        />
      </NuxtLink>
    </div>
    <!-- </a> -->
    <div class="logo-container" @click.capture="handleLogoClick">
      <img :src="wordmarkSource" class="logo | contain" />
      <!-- add in options for baseball softball tee-ball general -->
      <!-- <ul class="theme-selectors">
        <li
          v-for="theme in themes"
          :key="'theme-' + theme"
          class="theme-select"
        >
          <div>
            {{ theme }}
          </div>
        </li>
      </ul> -->
    </div>
    <ul v-show="navLinks" class="menu">
      <li v-for="item in navLinks" :key="item.display">
        <NuxtLink v-if="item.type === 'internal'" :to="item.path">
          {{ item.display }}
        </NuxtLink>
        <a v-if="item.type === 'external'" :href="item.path">{{
          item.display
        }}</a>
      </li>
    </ul>
  </div>
</template>
<script>
import wordmarkSources from '~/assets/wordmarkSources'
export default {
  name: 'TopNav',
  props: {
    selection: {
      required: false,
      type: String,
      default: () => 'baseball',
    },
  },
  data() {
    return {
      navLinks: [],
      topOfPage: true,
      scrollThreshold: 100,
      wideScreen: true,
      // themes: ['baseball', 'softball', 'tee-ball'],
    }
  },
  computed: {
    wordmarkSource() {
      return wordmarkSources[this.selection]
    },
  },
  async mounted() {
    const menuJSON = await this.$content('/json/menu-nav').fetch()
    this.navLinks = menuJSON.menu.filter((link) => !link.hidden)
    document.addEventListener('scroll', this.handleScroll)
    window.addEventListener('resize', this.handleResize)
    this.wideScreen = window.innerWidth > 800
  },
  destroyed() {
    document.removeEventListener('scroll', this.handleScroll)
    document.removeEventListener('resize', this.handleResize)
  },
  methods: {
    handleResize() {
      this.wideScreen = window.innerWidth > 800
    },
    handleScroll() {
      const scrollThreshold = 100
      this.topOfPage = window.pageYOffset < scrollThreshold
    },
    // handleRoundelHover(e: MouseEvent) {
    //   const target = e.target as HTMLElement
    //   target.classList.toggle('logo-large')
    // },
    // handleRoundelHover(e) {
    //   const target = e.target
    //   target.classList.toggle('logo-large')
    // },
    handleLogoClick() {
      this.$emit('showSelection')
    },
  },
}
</script>

<style scoped>
:root {
  box-sizing: border-box;
}
.navbar {
  /* --topOfPage: boolean */
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  height: calc(60px + 1em);
  border-bottom: 2px solid;
  border-bottom-color: var(--bottleGreen);
  background-color: hsl(90, 50%, 85%);
}
.navbar > * {
  flex: 0 0 33%;
}
.menu {
  display: flex;
  flex-direction: row;
  justify-content: right;
}
.menu > li {
  padding-right: 1em;
}
/* .contain {
  object-fit: contain;
} */
.roundel {
  width: 120px;
  max-width: 120px;
  min-width: 120px;
  aspect-ratio: 1;
  scale: 50%;
  translate: 0.5em 0.5em;
  transform-origin: 0 0;
}
.roundel-large {
  scale: 100%;
  translate: 1em 1em;
  transition: 0.3s ease-in-out;
}
#roundel-container {
  position: relative;
  width: 100%;
  min-height: 100%;
  margin: 0 auto 0 0;
}

.roundel {
  position: absolute;
  top: 0em;
  left: 0em;
  width: 100%;
  aspect-ratio: auto;
  box-shadow: 0 6px 6px 0px rgb(0 0 0 / 0.5);
  border-radius: 50%;
  transition: 0.3s ease-in-out;
}

.logo-container {
  text-align: center;
}
.logo {
  width: auto;
  height: 60px;
  transition: 0.3s ease-in-out;
  margin: auto;
}
.logo-large {
  transition: 0.3s ease-in-out;
  scale: 240%;
  translate: 0 80%;
}

@media screen and (max-width: 800px) {
  .logo-container {
    display: none;
  }
}
</style>
