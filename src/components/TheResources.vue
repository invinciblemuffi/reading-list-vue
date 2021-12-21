<template>
  <base-card>
    <base-button
      @click="setSelectedTab('stored-reading-list')"
      :mode="storedResourceBtnMode"
      >Stored Resources</base-button
    >
    <base-button
      @click="setSelectedTab('new-reading-resource')"
      :mode="addResourceBtnMode"
      >Add Resource</base-button
    >
  </base-card>
  <!-- Dynamic Component rendering based on selectedTab value - component tag from Vue -->
  <keep-alive>
    <component :is="selectedTab"></component>
  </keep-alive>
  <!-- We can use v-if to switch between the child components 
  imported here based on selectedTab value, 
  but dynamic component tag works too here -->
</template>

<script>
import StoredReadingList from "./StoredReadingList.vue";
import NewReadingResource from "./NewReadingResourse.vue";

export default {
  components: {
    StoredReadingList,
    NewReadingResource,
  },
  data() {
    return {
      selectedTab: "stored-reading-list",
      storedResources: [
        {
          id: "official-guide",
          title: "Become a Google Expert",
          description: "Learn how to Google",
          link: "https://google.com",
        },
        {
          id: "vue-guide",
          title: "Learn Vue",
          description: "Mastering Vue by creating apps.",
          link: "https://vuejs.org",
        },
      ],
    };
  },
  // Provide the data from here to all child components
  // i.e StoredReadingList and NewReadingResource will have access to this data.
  // Now in the above two child components I will inject the resources key to access the data provided
  provide() {
    return {
      resources: this.storedResources,
      addResource: this.addResource,
      deleteResource: this.removeResource,
    };
  },
  computed: {
    storedResourceBtnMode() {
      return this.selectedTab === "stored-reading-list" ? null : "flat";
    },
    addResourceBtnMode() {
      return this.selectedTab === "new-reading-resource" ? null : "flat";
    },
  },
  methods: {
    setSelectedTab(tabName) {
      this.selectedTab = tabName;
    },
    addResource(newResource) {
      this.storedResources.unshift(newResource);
      this.selectedTab = "stored-reading-list";
    },
    removeResource(resId) {
      const itemToRemoveId = this.storedResources.findIndex(
        (res) => res.id === resId
      );
      this.storedResources.splice(itemToRemoveId, 1);
    },
  },
};
</script>