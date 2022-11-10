<template>
  <div class="article-list">
    <h1>{{ pageTitle }}</h1>
    <ul>
      <li v-for="article in articles" :key="article.slug">
        <h2>{{ article.title }}</h2>
        <p>{{ article.description }}</p>
        <NuxtLink :to="`/${slug}/${article.slug}`">Read more...</NuxtLink>
      </li>
    </ul>
  </div>
</template>

<script>
const categories = ['news', 'info']
export default {
  async asyncData({ $content, params }) {
    console.log('pages/_slug.vue routine')
    const slug = params.slug
    let articles
    if (categories.includes(params.slug)) {
      articles = await $content(`/${params.slug}`).fetch()
    }
    console.log(params.slug)
    const pageTitle = categories.includes(params.slug)
      ? params.slug
      : 'articles'
    return { articles, pageTitle, slug }
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
</style>
