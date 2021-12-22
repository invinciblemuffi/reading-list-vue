<template>
  <h2>Add a new Resource to your Reading List</h2>
  <base-dialog
    v-if="inputIsInvalid"
    title="Invalid Input"
    @close="confirmError"
  >
    <!-- Unnamed slots can be referred by default keyword -->
    <template #default>
      <p>Unfortunately, one of the input fields is empty.</p>
      <p>
        Please check all input fields and make sure all fields have appropriate
        values.
      </p>
    </template>
    <template #actions>
      <base-button @click="confirmError">Okay</base-button>
    </template>
  </base-dialog>
  <base-card>
    <h3 v-if="isError">
      Sorry, Something went wrong, please try to save your resource again!
    </h3>
    <form @submit.prevent="submitData">
      <div class="form-control">
        <label for="title">Title</label>
        <input type="text" id="title" name="title" ref="titleInput" />
      </div>
      <div class="form-control">
        <label for="description">Description</label>
        <textarea
          id="description"
          name="description"
          rows="3"
          ref="descInput"
        />
      </div>
      <div class="form-control">
        <label for="link">Link</label>
        <input type="url" id="link" name="link" ref="linkInput" />
      </div>
      <div>
        <base-button type="submit">Add Resource</base-button>
      </div>
    </form>
  </base-card>
</template>

<script>
export default {
  inject: ["addResource"],
  data() {
    return {
      inputIsInvalid: false,
      isError: false,
    };
  },
  methods: {
    async submitData() {
      const enteredTitle = this.$refs.titleInput.value;
      const enteredDesc = this.$refs.descInput.value;
      const enteredLink = this.$refs.linkInput.value;

      if (
        enteredTitle.trim() === "" ||
        enteredDesc.trim() === "" ||
        enteredLink.trim() === ""
      ) {
        this.inputIsInvalid = true;
        return;
      }

      const newResource = {
        id: new Date().toISOString(),
        title: enteredTitle,
        description: enteredDesc,
        link: enteredLink,
      };
      console.dir(newResource);
      try {
        this.isError = false;
        // ReadingResource.json is added as it's a requirement for using firebase database
        const resp = await this.$http.post(
          "https://reading-list-app-f65aa-default-rtdb.asia-southeast1.firebasedatabase.app/ReadingResources.json",
          newResource
        );
        console.log(resp);
        if (resp.status === 200) {
          // Switch back to Reading Resource List and show the new object as well
          this.addResource(newResource);
        } else {
          throw new Error("Please try to add your resource again");
        }
        // Clearing out input fields
        this.$refs.titleInput.value = "";
        this.$refs.descInput.value = "";
        this.$refs.linkInput.value = "";
      } catch (error) {
        this.isError = true;
        console.log(error.message);
      }
    },
    confirmError() {
      this.inputIsInvalid = false;
    },
  },
};
</script>

<style scoped>
label {
  font-weight: bold;
  display: block;
  margin-bottom: 0.5rem;
}
input,
textarea {
  font: inherit;
  display: block;
  width: 100%;
  padding: 0.15rem;
  border: 1px solid #ccc;
}
input:focus,
textarea:focus {
  outline: none;
  border-color: #3a0061;
  background: #f7ebff;
}
.form-control {
  margin: 1rem 0;
}
</style>