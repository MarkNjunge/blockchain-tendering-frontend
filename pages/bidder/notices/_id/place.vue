<template>
  <div>
    <p>Place bid</p>
    <form @submit="submit" v-if="tenderNotice">
      <div class>
        <label for="tenderId">Tender Reference</label>
        <input
          type="text"
          name="tenderId"
          id="TenderId"
          v-bind:value="tenderNotice.tenderId"
          disabled
        />
      </div>

      <div class>
        <label for="tenderTitle">Tender Title</label>
        <input
          type="text"
          name="tenderTitle"
          id="tenderTitle"
          v-bind:value="tenderNotice.title"
          disabled
        />
      </div>
      <div class>
        <label for="bidSummary">Bid Summary</label>
        <input type="text" name="bidSummary" id="bidSummary" v-model="bidSummary" />
      </div>

      <div class>
        <label for="bidDocument">Bid Document</label>
        <input type="file" name="bidDocument" id="bidDocument" @change="onFileSelected" />
      </div>

      <div class v-for="(doc, index) in tenderNotice.requiredDocuments" :key="index">
        <div class>
          <label v-bind:for="doc">{{doc}}</label>
          <input type="file" v-bind:name="doc" v-bind:id="doc" @change="onFileSelected" />
        </div>
      </div>

      <button>Submit</button>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tenderNotice: null,
      bidSummary: null,
      uploadedDocs: {}
    };
  },
  async created() {
    const tenderId = this.$route.params.id;

    const res = await this.$axios({
      method: "GET",
      url: `/api/notices/${tenderId}`
    });

    this.tenderNotice = res.data;
  },
  methods: {
    onFileSelected(event) {
      const documentName = event.target.name;
      const file = event.target.files[0];

      if (documentName == "bidDocument") {
        this.bidDocument = file;
      } else {
        this.uploadedDocs[documentName] = file;
      }
    },
    async submit(e) {
      e.preventDefault();

      const bodyFormData = new FormData();
      bodyFormData.append("tenderId", this.tenderNotice.tenderId);
      bodyFormData.append("bidSummary", this.bidSummary);
      bodyFormData.append("bid", this.bidDocument);
      Object.keys(this.uploadedDocs).forEach(key => {
        bodyFormData.append(key, this.uploadedDocs[key]);
      });

      try {
        const res = await this.$axios({
          method: "post",
          url: "/api/bids",
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