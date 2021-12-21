<template>
  <ul>
    <reading-list
      v-for="res in resources"
      :key="res.id"
      :title="res.title"
      :description="res.description"
      :link="res.link"
    ></reading-list>
  </ul>
</template>

<script>
import ReadingList from "./ReadingList.vue";
export default {
  components: { ReadingList },
  //   props: ['resources']
  inject: ["resources"],
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: auto;
  padding: 0;
  max-width: 40rem;
}
</style>