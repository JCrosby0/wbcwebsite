<template>
  <div class="facebook-feed">
    <div class="center">
      For the latest news, events and results, come and find us on Facebook:
    </div>
    <div id="fb-root"></div>
    <script
      async
      defer
      crossorigin="anonymous"
      src="https://connect.facebook.net/en_GB/sdk.js#xfbml=1&version=v15.0"
      nonce="WFZyYg9G"
    ></script>
    <div
      class="fb-page"
      data-href="https://www.facebook.com/wbcwildcats"
      data-tabs="timeline"
      data-width="750"
      data-height="1000"
      data-small-header="false"
      data-adapt-container-width="true"
      data-hide-cover="false"
      data-show-facepile="false"
    >
      <blockquote
        cite="https://www.facebook.com/wbcwildcats"
        class="fb-xfbml-parse-ignore"
      >
        <a href="https://www.facebook.com/wbcwildcats">WBC Wildcats</a>
      </blockquote>
    </div>
  </div>
</template>
<script></script>
<style scoped>
.facebook-feed {
  text-align: center;
  width: 800px;
  margin: auto;
  padding: 2em;
}
</style>
