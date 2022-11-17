<template>
  <article>
    <h2><slot name="title">Post Title</slot></h2>
    <p>
      <slot
        >Lorem ipsum dolor sit amet consectetur adipisicing elit. Itaque
        repellendus enim pariatur, unde voluptatibus error maxime in.
      </slot>
    </p>
    <slot name="link">
      <a href="#">Read more...</a>
    </slot>
  </article>
</template>

<script>
export default {
  name: 'PanelPost',
  data() {
    return {}
  },
}
</script>

<style scoped>
article {
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  background: none;
  background-image: ;
}

h2 {
  font-size: x-large;
}
a {
  border-color: var(--bottle-green);
  border: 1px solid;
  width: max-content;
  padding: 0.25em 0.5em;
  margin-top: 0.5em;
  background: white;
}
</style>
