<template>
  <div class="">
    <div v-if="articles" class="article-list">
      <h1>{{ pageTitle }}</h1>
      <ul>
        <li
          v-for="article in articles"
          :key="article.slug"
          :style="{
            '--url': `url('${article.img}')`,
          }"
        >
          <h2>{{ article.title }}</h2>
          <p>{{ article.description }}</p>
          <NuxtLink :to="`/${category}/${article.slug}`"
            ><button>Read more...</button>
          </NuxtLink>
        </li>
      </ul>
    </div>
    <!-- <div v-if="article" class="article">
      <article class="prose lg:prose-xl">
        <nuxt-content :document="article" />
      </article>
    </div> -->
  </div>
</template>

<script>
const categories = ['news', 'info', 'about']
export default {
  async asyncData({ $content, params }) {
    let article
    let articles
    const slug = params.slug
    const category = params.category
    if (params.category && params.slug) {
      // this should be covered by _slug.vue
      // article = await $content(`/${params.category}/${params.slug}`).fetch()
      // console.log('We are in pages category slug article')
    } else if (categories.includes(params.category)) {
      articles = await $content(`${params.category}`).fetch()
      articles = articles.filter((article) => article.published)
      articles.forEach((article) => {
        article.img = require(`~/assets/img/${article.image}`)
      })
      console.log('We are in pages category slug articles')
    } else {
      articles = []
    }

    const pageTitle = categories.includes(params.category)
      ? params.category
      : 'articles'
    /// //////////////////////////////////
    if (categories.includes(params.slug)) {
      articles = await $content(`${params.slug}`).fetch()
    }

    return { article, articles, pageTitle, category, slug }
  },
  methods: {
    makeBackgroundImage(url) {
      const bgImageValue = `linear-gradient(rgb(0 0 0 / 0) 50%, rgb(255, 255, 255) 65%), url('${url}')`
      return bgImageValue
    },
  },
}
</script>

<style scoped>
h1 {
  font-size: xx-large;
  text-transform: capitalize;
  padding-bottom: 1em;
}
h2 {
  font-size: large;
  font-weight: bold;
}
.article-list {
  padding: 2rem;
  max-width: 800px;
  margin: auto;
}
ul {
  display: flex;
  flex-wrap: wrap;
  gap: 2em;
  justify-content: space-evenly;
  align-items: center;
}
li {
  padding: 1rem;
  border: 1px var(--bottle-green) solid;
  border-radius: 0.5em;
  width: clamp(300px, 40%, calc(50%-1em));
  min-height: 400px;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  background-size: cover, auto 80%, auto 80%;
  background-position: center, 49.9% 0, 49.9% 0;
  background-repeat: no-repeat;
  background-image: linear-gradient(
      rgb(0 0 0 / 0) 49.9%,
      rgb(255, 255, 255) 65%
    ),
    var(--url);
}
button {
  margin-top: 1em;
  font-size: small;
  padding: 0.5em 1em;
  border: 1px var(--bottle-green) solid;
  border-radius: 0.5em;
}
</style>
