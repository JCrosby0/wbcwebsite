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
        <div><i> Welcome to the home of the </i></div>
        <img
          src="~/assets/img/willettonWildcats.png"
          alt="Willetton Wildcats"
          class="ww-logo"
        />
      </div>
      <!-- content panels -->
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
    <div class="panel-group">
      <PanelPostVue
        v-for="(panel, pi) in panels"
        :key="'panel' + pi"
        class="panel"
        :style="{
          '--url': `url(${panel.img})`,
        }"
      >
        <template #title>{{ panel.title }}</template>
        <template>{{ panel.description }}</template>
        <template #link>
          <NuxtLink v-if="panel.type === 'internal'" :to="panel.path">
            Read more...
          </NuxtLink>
          <a v-if="panel.type === 'external'" :href="panel.path"
            >Read more...</a
          >
        </template>
      </PanelPostVue>
    </div>
  </main>
</template>

<script>
import PanelPostVue from '~/components/PanelPost.vue'

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

export default {
  name: 'IndexPage',
  components: {
    PanelPostVue,
  },
  data() {
    return {
      panels: [],
    }
  },
  computed: {
    heroImage() {
      if (!this || !this.$parent || !this.$parent.$parent) return
      const layout = this.$parent.$parent
      const selection = layout.selectedDivision
      const key = selection || 'baseball'
      // const key = (this.selection as keyof typeof heroImages) || 'baseball'
      return heroImages[key].img
    },
  },
  async mounted() {
    const panelsJSON = await this.$content('json/panels').fetch()
    this.panels = panelsJSON.panels.filter((p) => !p.hidden)
    this.panels.forEach((panel) => {
      panel.img = require(`~/assets/img/${panel.url}`)
    })
  },
}
</script>

<style scoped>
main {
  --header-height: calc(60px + 1em);
  --fold-height: 20vh;
  --hero-height: calc(100vh - var(--header-height) - var(--fold-height));
}
.container {
  max-width: 800px;
  margin: auto;
  /* background-color: #ddd; */
  /* min-height: 100%; */
  padding: 0 2em;
}
.hero {
  height: var(--hero-height);
  width: 100%;
  background-image: url(assets/img/wtcStates.jpg);
  background-image: var(--url);
  background-size: cover;
  background-position: center;
  position: relative;
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
  padding: 1em;
}
.welcome > .heading {
  flex: 0 0 20vh;
  margin: auto;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: flex-start;
}
.welcome > .body {
  flex: 1 1 auto;
}
.wbc {
  color: var(--bottle-green);
  font-weight: bold;
}
.ww-logo {
  padding-bottom: 1em;
}

.panel-group {
  position: absolute;
  height: var(--hero-height);
  top: 2em;
  right: 2em;
  display: flex;
  flex-wrap: wrap-reverse;
  flex-direction: column;
  justify-content: center;
  gap: 2em;
}
.panel {
  border-colour: var(--bottle-green);
  border: 1px solid;
  width: 300px;
  height: 250px;
  box-shadow: 1em 1em 1em 0 rgb(0 0 0 / 0.75);
  padding: 1em;
  background-image: linear-gradient(
      30deg,
      rgb(255 213 5 / 0.85) 20%,
      rgb(255 255 255 / 0.75) 40%,
      rgb(0 0 0 / 0) 60%
    ),
    var(--url);
  background-size: cover;
  background-position: top right;
  transition: 0.3s ease-in-out;
}
.panel:hover {
  scale: 1.15;
}
@media screen and (max-width: 400px) {
  .panel-group {
    position: relative;
    align-items: center;
    right: 0;
    top: 0;
    gap: 0.5em;
    padding: 0.5em;
  }
  .panel {
    box-shadow: none;
    border: none;
    /* background: none; */
    width: 100%;
  }
  .panel:hover {
    scale: none;
  }
}
@media screen and (max-width: 800px) {
  .panel-group {
    flex-wrap: nowrap;
  }
}
</style>
