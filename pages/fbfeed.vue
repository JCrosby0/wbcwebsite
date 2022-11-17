<template>
  <main>
    <!-- <script>
      window.fbAsyncInit = function () {
        FB.init({
          appId: 450442840363350,
          access_token:
          autoLogAppEvents: true,
          xfbml: true,
          version: 'v15.0',
        })
      }
    </script> -->
    <h1>Latest Results</h1>
    <button @click="handleButtonClick">Manually Retrieve Post</button>
    <p :style="{ whiteSpace: 'pre-wrap' }">{{ postMessage }}</p>
    <!-- <div>Latest Attachment: {{ attachments }}</div> -->
    <img
      v-for="(img, index) in postImages"
      :key="'image-' + index"
      :src="img.src"
      :height="img.height"
      :width="img.width"
    />
  </main>
</template>

<script>
// const appId = process.env.FB_APP_ID

// connect to FB API
const fbAccessConfig = {
  appID: process.env.FB_APP_ID, // WBC Wildcats by ib.jaycee
  userID: '', // Joe Crosby
  pageID: process.env.FB_PAGE_ID, // WBC Wildcats
  credentialsPassword: '',
  accessToken: '',
  page_access_token: '',
  pageName: process.env.FB_PAGE_NAME,
  appAccessToken: process.env.FB_APP_ACCESS_TOKEN,
}
const fbInitConfig = {
  appId: process.env.FB_APP_ID,
  access_token: process.env.FB_APP_ACCESS_TOKEN,
  autoLogAppEvents: true,
  status: true,
  xfbml: false,
  version: 'v15.0',
}
// get page data for the user
function getUserPageData(config) {
  return new Promise((resolve, reject) => {
    FB.api(
      `/${config.userID}/accounts/?access_token=${config.accessToken}`,
      'GET',
      (response) => {
        if (!response.data || response.data.length === 0) {
          reject(new Error('no data'))
        }
        const wbc = response.data.find((d) => d.id === config.pageID)
        if (!wbc) reject(new Error('insufficient data returned'))
        fbAccessConfig.page_access_token = wbc.access_token
        fbAccessConfig.pageID = wbc.id
        resolve(response)
      }
    )
  })
}

// get a page token -- we already get this as part of get UserPage Data
// function getPageToken() {
//   return new Promise((resolve, reject) => {
//     let token = ''
//     FB.api(`/${fbAccessConfig.pageID}`, 'GET', (response) => {
//       console.log('  response to page token request', response)
//       if (!response.access_token) reject(new Error('no token'))
//       token = response.access_token
//       resolve(token)
//     })
//   })
// }

// FB API call: getPageFeed()
function getPageFeed(config) {
  return new Promise((resolve, reject) => {
    FB.api(
      `/${config.pageID}/feed`,
      'GET',
      { access_token: config.page_access_token },
      (response) => {
        if (!response.data || response.data.length === 0) {
          reject(new Error('no feed data'))
        }
        resolve(response)
      }
    )
  })
}
// FB API call: getPost(postID)
function getPost(config, postID) {
  return new Promise((resolve, reject) => {
    FB.api(
      `/${postID}`,
      'GET',
      { access_token: config.page_access_token },
      (responsePost) => {
        if (responsePost) {
          resolve(responsePost)
        } else {
          reject(new Error('invalid post response'))
        }
      }
    )
  })
}
// FB API call: getPostAttachments(postID)
function getPostAttachments(config, postID) {
  return new Promise((resolve, reject) => {
    FB.api(
      `/${postID}/attachments`,
      'GET',
      { access_token: config.page_access_token },
      (responsePostAttachments) => {
        if (responsePostAttachments) {
          resolve(responsePostAttachments)
        } else {
          reject(new Error('invalid attachments'))
        }
      }
    )
  })
}
// FB API call: getLoginStatus()
function getLoginStatus() {
  return new Promise((resolve, reject) => {
    FB.getLoginStatus((response) => {
      if (response.status === 'connected') {
        resolve(response)
      } else {
        reject(new Error('not connected'))
      }
    })
  })
}
// FB API call: login
function fbLogin() {
  return new Promise((resolve, reject) => {
    FB.login((response) => {
      if (response.authResponse !== null) {
        resolve(response)
      } else {
        reject(new Error('user abandonded login'))
      }
    })
  })
}
// function: check's for login status, specifically access token, if not present, logs in to generate one.
async function doLogin() {
  const response = await getLoginStatus()
    .then((result) => {
      // response = result
      return result
    })
    .catch(() => {
      // not connected, login
      return fbLogin()
    })
    .then((result) => {
      // response = result
      console.log('logged in', result)
      return result
    })
  console.log('  response before set Credentials: ', response)
  setCredentials(response)
  return response
}
const setCredentials = (response) => {
  if (!response || !response.authResponse) {
    console.log('  no credentials received')
    return
  }
  console.log('  Credentials received, setting: ', response)
  const authResponse = response.authResponse
  fbAccessConfig.accessToken = authResponse.accessToken
  fbAccessConfig.userID = authResponse.userID
}

async function doRoutine(context) {
  // needs to be first
  await doLogin()
  await getUserPageData(fbAccessConfig)
  // const pageToken = await getPageToken()
  const pageFeed = await getPageFeed(fbAccessConfig)
  const index = 0
  const postID = pageFeed.data[index].id
  const pageFirstPost = await getPost(fbAccessConfig, postID)
  const postAttachments = await getPostAttachments(fbAccessConfig, postID)
  context.post = pageFirstPost
  context.attachments = postAttachments
  return [pageFirstPost, postAttachments]
}
const FBInitFunction = async (context = this.content) => {
  const FB = window.FB
  await FB.init(fbInitConfig)
  return await doRoutine(context)
}

// will execute once the sdk is loaded

export default {
  data() {
    return {
      content: {
        post: {},
        attachments: {},
      },
    }
  },
  head() {
    return {
      script: [
        {
          src: 'https://connect.facebook.net/en_US/sdk.js',
          async: true,
          defer: false,
          crossorigin: 'anonymous',
        },
      ],
    }
  },
  computed: {
    postMessage() {
      if (!this.content.post) return ''
      return this.content.post.message
    },
    postImages() {
      if (!this.content.attachments.data) {
        return []
      }
      const images = []
      const mainImage = this.content.attachments.data[0].media.image
      images.push({
        src: mainImage.src,
        height: mainImage.height,
        width: mainImage.width,
      })
      this.content.attachments.data[0].subattachments.data.forEach((img) => {
        images.push({
          src: img.media.image.src,
          height: img.media.image.height,
          width: img.media.image.width,
        })
      })
      return images
    },
  },
  mounted() {
    window.FB.init(fbInitConfig)
    window.fbAsyncInit = FBInitFunction(this.content)
  },
  methods: {
    async handleButtonClick() {
      await FBInitFunction(this.content)
    },
  },
}
</script>

<style scoped>
main {
  width: 800px;
  margin: auto;
  text-align: left;
}
h1 {
  font-size: xx-large;
}
button {
  padding: 0.5em 1em;
  border: 1px grey solid;
}
</style>
