<template>
  <li :id="id">
    <base-card>
      <header>
        <h3>{{ title }}</h3>
        <base-button mode="flat" @click="deleteResource(id)"
          >Delete</base-button
        >
      </header>
      <p>{{ description }}</p>
      <nav><a :href="link" target="_blank">Learn More</a></nav>
    </base-card>
  </li>
</template>

<script>
export default {
  inject: ["deleteResource"],
  props: {
    id: String,
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