<template>
  <div class="background" :height="height">
    <div class="modal">
      <p>Select a division to filter content:</p>

      <div class="image-container">
        <img
          src="~/assets/img/willetton_bc_wordmark_baseball.png"
          alt="Willetton Baseball Logo"
          width="100px"
          :style="{ selected: selection === 'baseball' }"
          @click="makeSelection('baseball')"
        />
        <img
          src="~/assets/img/willetton_bc_wordmark_softball.png"
          alt="Willetton
          Softball Logo"
          width="100px"
          :style="{ selected: selection === 'softball' }"
          @click="makeSelection('softball')"
        /><img
          src="~/assets/img/willetton_bc_wordmark_teeball.png"
          alt="Willetton
          Tee-ball Logo"
          width="100px"
          :style="{ selected: selection === 'teeball' }"
          @click="makeSelection('teeball')"
        />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    selection: {
      required: false,
      type: String,
      default: () => 'baseball',
    },
  },
  data() {
    return {
      height: null,
    }
  },
  mounted() {
    this.height = document.body.scrollHeight
  },

  methods: {
    makeSelection(payload) {
      this.$emit('selection', payload)
    },
  },
}
</script>

<style scoped>
.background {
  /* background-color: rgb(0 0 0 / 0.75);
  width: 100vw;
  top: 0;
  left: 0;
  position: absolute; */
}
.modal {
  background-color: rgb(255 255 255 / 0.75);
  border: 1px solid;
  border-color: var(--bottle-green);
  border-radius: 0.5em;
  padding: 2em;
  width: 75%;
  /* height: 75%; */
  margin: auto;
  text-align: center;
  position: fixed;
  top: 75px;
  left: 50%;
  translate: -50% 0;
}
.image-container {
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
}
.image-container > img {
  width: 200px;
}
p {
  padding-bottom: 1em;
}

img.hover {
  border: 2px solid;
  border-colour: var(--gold);
}
.selected {
  border: 2px solid;
  border-color: var(--bottle-green);
}
</style>
