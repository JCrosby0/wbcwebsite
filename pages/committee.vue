<template>
  <main>
    <h1>2022/23 Committee</h1>
    {{ committeeList }}
  </main>
</template>
<script>
export default {
  name: 'Committee',
  data() {
    return {
      committeeList: [],
    }
  },
  async mounted() {
    const committeeJSON = await this.$content('json/committee').fetch()
    this.committeeList = committeeJSON.committee
  },
}
</script>
<style></style>
