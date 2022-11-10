<template>
  <div class="main">
    <div v-if="articles" class="article-list">
      <!-- <ul>
        <li v-for="article in articles" :key="article.slug">
          <h2>{{ article.title }}</h2>
          <p>{{ article.description }}</p>
          <NuxtLink :to="`/${category}/${article.slug}`">Read more...</NuxtLink>
        </li>
      </ul> -->
    </div>
    <div v-if="article" class="article">
      <article class="prose lg:prose-base prose-slate">
        <h1>{{ article.title }}</h1>
        <div class="right">
          <NuxtLink :to="`/${category}`"> Back to Articles </NuxtLink>
        </div>
        <nuxt-content :document="article" />
        <div class="right">
          <NuxtLink :to="`/${category}`"> Back to Articles </NuxtLink>
        </div>
      </article>
    </div>
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
      article = await $content(`/${params.category}/${params.slug}`).fetch()
      console.log('We are in pages category slug article')
    } else if (categories.includes(params.category)) {
      articles = await $content(`/${params.category}`).fetch()
      console.log('We are in pages category slug articles')
    } else {
      articles = []
    }
    const pageTitle = categories.includes(params.category)
      ? params.category
      : 'articles'
    /// //////////////////////////////////
    if (categories.includes(params.slug)) {
      articles = await $content(`/${params.slug}`).fetch()
    }

    return { article, articles, pageTitle, category, slug }
  },
}
</script>

<style scoped>
h1 {
  font-size: x-large;
  text-transform: capitalize;
}
h2 {
  font-size: large;
  font-weight: bold;
}
.article-list {
  padding: 2rem;
  width: 800px;
  margin: auto;
}
li {
  padding: 1rem;
  border: 1px grey solid;
  margin-bottom: 1rem;
}
article {
  width: 800px;
  margin: auto;
  padding: 1rem;
}
.right {
  text-align: right;
}
</style>
