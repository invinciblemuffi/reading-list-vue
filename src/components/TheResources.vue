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
  <teleport to="body">
    <base-dialog
      v-if="serverError"
      title="Oops, Sorry!"
      @close="reloadComponent"
    >
      <template #default>
        <p>We were unable to get your data.</p>
        <p>
          Let me try once again to get the data, please close this window and
          wait a moment!
        </p>
      </template>
    </base-dialog>
  </teleport>
</template>

<script>
import StoredReadingList from "./StoredReadingList.vue";
import NewReadingResource from "./NewReadingResourse.vue";

export default {
  components: {
    StoredReadingList,
    NewReadingResource,
  },
  async mounted() {
    // this.$http points to Axios which is cofigured in main.js file so that entire Vue App can use it globally
    const resp = await this.$http.get(
      "https://reading-list-app-f65aa-default-rtdb.asia-southeast1.firebasedatabase.app/ReadingResources.json"
    );
    // Only resp will do null and undefined check
    if (resp && Object.keys(resp).length === 0) {
      return (this.serverError = true);
    }
    this.storedResources.unshift(...Object.values(resp.data));
    console.log(this.storedResources);
    this.serverError = false;
  },
  data() {
    return {
      serverError: false,
      selectedTab: "stored-reading-list",
      storedResources: [
        {
          id: "official-guide",
          title: "Become a Google Search Expert",
          description: "Learn how to search in Google",
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
    reloadComponent() {
      this.serverError = true;
    },
  },
};
</script>