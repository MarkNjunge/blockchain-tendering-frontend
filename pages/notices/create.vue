<template>
  <div>
    <h1>Create notice</h1>
    <form @submit="submit">
      <div class>
        <label for="ref">Reference Number</label>
        <input type="text" id="ref" name="ref" v-model="ref" required />
      </div>
      <div class>
        <label for="title">Title</label>
        <input type="text" id="title" name="title" v-model="title" required />
      </div>
      <div class>
        <label for="tenderDocument">Tender Document</label>
        <input type="file" name="card" id="card" @change="onFileSelected" />
      </div>
      <div class>
        <label for="requiredDocuments">Required Documents</label>
        <input
          type="text"
          id="requiredDocuments"
          name="requiredDocuments"
          placeholder="Enter a list separated by commas"
          v-model="requiredDocuments"
          required
        />
      </div>
      <div class>
        <label for="closingDate">Closing Date</label>
        <input type="date" name="closingDate" id="closingDate " v-model="closingDate" required />
      </div>
      <div class>
        <label for="openingVenue">Opening Venue</label>
        <input type="text" name="openingVenue" id="openingVenue" v-model="openingVenue" required />
      </div>
      <div class>
        <label for="openingDate">Opening Date</label>
        <input type="date" name="openingDate" id="openingDate" v-model="openingDate" required />
      </div>
      <button type="submit">Publish</button>
    </form>
  </div>
</template>

<script>
export default {
  methods: {
    onFileSelected(event) {
      this.tenderDocument = event.target.files[0];
    },
    async submit(e) {
      e.preventDefault();

      if (!this.tenderDocument) {
        return;
      }

      const bodyFormData = new FormData();
      bodyFormData.append("id", this.ref);
      bodyFormData.append("document", this.tenderDocument);
      bodyFormData.append("title", this.title);
      bodyFormData.append(
        "requiredDocuments",
        JSON.stringify(this.requiredDocuments.split(",").map(v => v.trim()))
      );
      bodyFormData.append("closingDate", this.closingDate);
      bodyFormData.append("openingVenue", this.openingVenue);
      bodyFormData.append("openingDate", this.openingDate);
      try {
        const res = await this.$axios({
          method: "post",
          url: "/api/notices",
          data: bodyFormData,
          config: { headers: { "Content-Type": "multipart/form-data" } }
        });

        this.$nuxt.$router.replace({ path: "/dashboard" });
      } catch (e) {
        console.log(e);
        alert(e.message);
      }
    }
  }
};
</script>