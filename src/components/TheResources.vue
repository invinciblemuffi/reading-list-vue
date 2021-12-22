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
  <h2 v-if="isLoading">Loading...</h2>
  <h2
    v-else-if="!isLoading && (!storedResources || storedResources.length === 0)"
  >
    Please add some Reading Resources to your List
  </h2>
  <!-- Dynamic Component rendering based on selectedTab value - component tag from Vue -->
  <keep-alive
    v-else-if="!isLoading && storedResources && storedResources.length > 0"
  >
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
        <p>A server error occured on our end!</p>
        <p>Please wait for a few seconds and try again.</p>
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
    try {
      this.isLoading = true;
      // this.$http points to Axios which is cofigured in main.js file so that entire Vue App can use it globally
      const resp = await this.$http.get(
        "https://reading-list-app-f65aa-default-rtdb.asia-southeast1.firebasedatabase.app/ReadingResources.json"
      );
      console.log(resp);
      // Only resp will do null and undefined check
      if (resp && Object.keys(resp).length === 0) {
        return (this.serverError = true);
      } else {
        for (let key in resp.data) {
          let title = resp.data[key].title;
          let desc = resp.data[key].description;
          let link = resp.data[key].link;
          // console.log(key, title, desc, link);
          let serverData = { id: key, title, description: desc, link };
          this.storedResources.unshift(serverData);
        }
        // this.storedResources.unshift(...Object.values(resp.data));
        console.log(this.storedResources);
        this.serverError = false;
        // this.storedResources.splice(0, this.storedResources.length);
        this.isLoading = false;
      }
    } catch (error) {
      // Handling Server errors where
      this.serverError = true;
    }
  },
  data() {
    return {
      serverError: false,
      isLoading: false,
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
    async removeResource(resId) {
      try {
        const itemToRemove = this.storedResources.find(
          (res) => res.id === resId
        );
        console.log(itemToRemove);
        const resp = await this.$http.delete(
          `https://reading-list-app-f65aa-default-rtdb.asia-southeast1.firebasedatabase.app/ReadingResources/${itemToRemove.id}.json`
        );
        if (resp.status === 200) {
          this.serverError = false;
          this.storedResources.splice(itemToRemove.id, 1);
        } else {
          this.serverError = true;
        }
      } catch (error) {
        this.serverError = true;
        console.log(error.message);
      }
    },
    reloadComponent() {
      this.serverError = false;
    },
  },
};
</script>