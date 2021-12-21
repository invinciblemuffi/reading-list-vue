<template>
  <li>
    <base-card>
      <header>
        <h3>{{ title }}</h3>
        <base-button mode="flat">Delete</base-button>
      </header>
      <p>{{ description }}</p>
      <nav><a :href="link" target="_blank">Learn More</a></nav>
    </base-card>
  </li>
</template>

<script>
export default {
  props: {
    title: String,
    description: String,
    link: String,
  },
};
</script>

<style scoped>
li {
  margin: auto;
  max-width: 40rem;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>