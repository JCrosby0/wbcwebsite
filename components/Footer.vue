<template>
  <div class="footer | column">
    <div class="site-title">
      <div class="footer-img-container">
        <img class="footer-img | contain" :src="wordmarkSource" />
      </div>
    </div>
    <div class="nav-links">
      <ul v-show="navLinks" class="menu | row">
        <li v-for="item in navLinks" :key="item.display">
          <NuxtLink v-if="item.type === 'internal'" :to="item.path"
            >{{ item.display }}
          </NuxtLink>
          <a v-if="item.type === 'external'" :href="item.path">{{
            item.display
          }}</a>
        </li>
      </ul>
    </div>
    <div class="social-media">
      <ul v-show="smLinks" class="smlinks | row">
        <li v-for="item in smLinks" :key="item.display">
          <NuxtLink v-if="item.type === 'internal'" :to="item.path"
            >{{ item.display }}
          </NuxtLink>
          <a v-if="item.type === 'external'" :href="item.path">{{
            item.display
          }}</a>
        </li>
      </ul>
    </div>
    <div class="copy">&copy; 2022 Willetton Baseball Club (Inc)</div>
    <!-- <div class="disclaimer">
      <ul v-show="smLinks" class="smlinks | row">
        <li v-for="item in discLinks" :key="item.display">
          <NuxtLink v-if="item.type === 'internal'" :to="item.path"
            >{{ item.display }}
          </NuxtLink>
          <a v-if="item.type === 'external'" :href="item.path">{{
            item.display
          }}</a>
        </li>
      </ul>
    </div> -->
  </div>
</template>
<script>
import wordmarkSources from '~/assets/wordmarkSources'
export default {
  name: 'FooterNav',
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
      smLinks: [],
      discLinks: [],
      // themes: ['baseball', 'softball', 'tee-ball'],
    }
  },
  computed: {
    wordmarkSource() {
      return wordmarkSources[this.selection]
    },
  },
  async mounted() {
    const menuJSON = await this.$content('json/menu-nav').fetch()
    const smJSON = await this.$content('json/menu-social-media').fetch()
    const discJSON = await this.$content('json/menu-disclaimer').fetch()
    this.navLinks = menuJSON.menu.filter((link) => !link.hidden)
    this.smLinks = smJSON.links.filter((link) => !link.hidden)
    this.discLinks = discJSON.menu.filter((link) => !link.hidden)
  },
}
</script>

<style scoped>
.footer {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  background-color: hsl(90, 100%, 85%);
  padding: 1em;
  border-top: 1px solid;
  border-top-colour: var(--bottleGreen);
  font-size: small;
}
.footer-img {
  width: auto;
  height: 60px;
  transition: 0.3s ease-in-out;
  margin: 1em;
}
.contain {
  object-fit: contain;
}
.column {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.row {
  display: flex;
  flex-direction: row;
}

li + li::before {
  content: '|';
  padding: 0 1em;
}
</style>
