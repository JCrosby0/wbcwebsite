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
  appID: '450442840363350', // WBC Wildcats by ib.jaycee
  userID: '', // Joe Crosby
  pageID: '235493236635117', // WBC Wildcats
  credentialsPassword: '',
  accessToken: '',
  page_access_token: '',
  pageName: 'WBC Wildcats',
}
const fbInitConfig = {
  appId: 450442840363350,
  // access_token: '',
  autoLogAppEvents: true,
  status: true,
  xfbml: false,
  version: 'v15.0',
}
const FBInitFunction = async (context) => {
  const FB = window.FB

  // function: check's for login status, specifically access token, if not present, logs in to generate one.
  async function doLogin() {
    let response
    await FB.getLoginStatus((statusResponse) => {
      console.log('  statusResponse : ', statusResponse)
      if (statusResponse.status === 'connected') {
        response = statusResponse
      }
    })
    // don't try to log in if we're already connected
    if (response === undefined) {
      await FB.login((loginResponse) => {
        if (loginResponse.authResponse !== null) {
          console.log('  loginResponse: ', loginResponse)
          response = loginResponse

          // do the rest of the stuff in here?
        }
      })
    }
    console.log('  response: ', response)
    setCredentials(response)
    return response
  }

  // helper function to set the credentials in a local object from the auth response
  const setCredentials = (response) => {
    if (!response || !response.authResponse) {
      console.log('  no credentials received')
      return
    }
    console.log('  Credentials received, setting: ', response)
    const authResponse = response.authResponse
    fbAccessConfig.accessToken = authResponse.accessToken
    fbAccessConfig.userID = authResponse.userID
    console.log('  fbAccessConfig: ', fbAccessConfig)
  }

  // get page data for the user
  function getUserPageData() {
    return new Promise((resolve, reject) => {
      console.log('  getting page data for the user')
      FB.api(
        `/${fbAccessConfig.userID}/accounts/?access_token=${fbAccessConfig.accessToken}`,
        'GET',
        (response) => {
          console.log('  response to Page Access token request', response)
          if (!response.data || response.data.length === 0) {
            reject(new Error('no data'))
          }
          const wbc = response.data.find(
            (d) => d.name === fbAccessConfig.pageName
          )
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

  // get page feed
  function getPageFeed() {
    return new Promise((resolve, reject) => {
      FB.api(
        `/${fbAccessConfig.pageID}/feed`,
        'GET',
        { access_token: fbAccessConfig.page_access_token },
        (response) => {
          console.log('  response to feed request: ', response)
          if (!response.data || response.data.length === 0) {
            reject(new Error('no feed data'))
          }
          resolve(response)
        }
      )
    })
  }
  function getPost(postID) {
    return new Promise((resolve, reject) => {
      FB.api(
        `/${postID}`,
        'GET',
        { access_token: fbAccessConfig.page_access_token },
        (responsePost) => {
          console.log('  response to page_post request: ', responsePost)
          resolve(responsePost)
        }
      )
    })
  }
  function getPostAttachments(postID) {
    return new Promise((resolve, reject) => {
      FB.api(
        `/${postID}/attachments`,
        'GET',
        { access_token: fbAccessConfig.page_access_token },
        (responsePostAttachments) => {
          console.log(
            '  response to page_post_attachments request: ',
            responsePostAttachments
          )
          resolve(responsePostAttachments)
        }
      )
    })
  }
  await FB.init(fbInitConfig)
  // wrap everything in an async function
  async function doRoutine() {
    // needs to be first
    console.log('[1] Logging in')
    console.groupCollapsed('login')
    const loginResult = await doLogin()
    console.groupEnd()
    console.log('loginResult: ', loginResult)
    console.log('[2] Retrieving User Page Data')
    console.groupCollapsed('userPageData')
    const userPageData = await getUserPageData()
    console.groupEnd()
    console.log('userPageData: ', userPageData)
    // console.log('[3]: Getting page token')
    // const pageToken = await getPageToken()
    // console.log('pageToken: ', pageToken)
    console.log('[4]: Getting page feed')
    console.groupCollapsed('feed')
    const pageFeed = await getPageFeed()
    console.groupEnd()
    console.log('pageFeed: ', pageFeed)
    console.log('[5]: Getting first post')
    console.groupCollapsed('post')
    const index = 0
    const postID = pageFeed.data[index].id
    const pageFirstPost = await getPost(postID)
    const postAttachments = await getPostAttachments(postID)
    console.groupEnd()
    console.log('pageFirstPost: ', pageFirstPost)
    console.log('postAttachments: ', postAttachments)
    context.post = pageFirstPost
    context.attachments = postAttachments
    return [pageFirstPost, postAttachments]
  }
  // BEGIN FUNCTION CALLS

  return await doRoutine()
}

// will execute once the sdk is loaded

export default {
  data() {
    return {
      post: {},
      attachments: {},
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
      if (!this.post) return ''
      return this.post.message
    },
    postImages() {
      if (!this.attachments.data) {
        return []
      }
      const images = []
      const mainImage = this.attachments.data[0].media.image
      images.push({
        src: mainImage.src,
        height: mainImage.height,
        width: mainImage.width,
      })
      this.attachments.data[0].subattachments.data.forEach((img) => {
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
    window.fbAsyncInit = FBInitFunction(this)
  },
  methods: {
    async handleButtonClick() {
      let posts, attachments
      ;[posts, attachments] = await FBInitFunction()
      this.post = posts
      this.attachments = attachments
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
