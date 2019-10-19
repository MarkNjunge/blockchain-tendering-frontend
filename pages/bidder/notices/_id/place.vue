<template>
  <div class="container mx-auto px-4 xl:w-1/2 md:w-4/5">
    <nuxt-link to="/organization" class="up-navigation">
      <svg class="up-navigation-icon">
        <use href="#back" />
      </svg>
      <p class="up-navigation-text">BACK</p>
    </nuxt-link>
    <h1 class="section-title mt-2">Place a Bid</h1>
    <form @submit="submit" v-if="tenderNotice" class="mt-2 mb-8 card">
      <div class="input-group">
        <label for="tenderId" class="input-label">Tender Reference</label>
        <input
          type="text"
          name="tenderId"
          id="TenderId"
          v-bind:value="tenderNotice.tenderId"
          class="input-field-disabled"
          disabled
        />
      </div>

      <div class="mt-3 input-group">
        <label for="tenderTitle" class="input-label">Tender Title</label>
        <input
          type="text"
          name="tenderTitle"
          id="tenderTitle"
          v-bind:value="tenderNotice.title"
          class="input-field-disabled"
          disabled
        />
      </div>
      <div class="mt-3 input-group">
        <label for="bidSummary" class="input-label">Bid Summary</label>
        <input
          type="text"
          name="bidSummary"
          id="bidSummary"
          v-model="bidSummary"
          class="input-field"
        />
      </div>

      <div class="mt-3 input-group">
        <label for="bidDocument" class="input-label">Bid Document</label>
        <input
          type="file"
          name="bidDocument"
          id="bidDocument"
          @change="onFileSelected"
          class="input-field"
        />
      </div>

      <div
        class="mt-3 input-group"
        v-for="(doc, index) in tenderNotice.requiredDocuments"
        :key="index"
      >
        <label v-bind:for="doc.ref" class="input-label">{{doc.name}}</label>
        <input
          type="file"
          v-bind:name="doc.ref"
          v-bind:id="doc.ref"
          @change="onFileSelected"
          class="input-field"
        />
      </div>

      <button class="mt-6 w-full btn">Submit</button>
    </form>
  </div>
</template>

<script>
String.prototype.capitalize = function() {
  return this.replace(/(?:^|\s)\S/g, function(a) {
    return a.toUpperCase();
  });
};

export default {
  data() {
    return {
      tenderNotice: null,
      bidSummary: null,
      uploadedDocs: {}
    };
  },
  async created() {
    this.noticeId = decodeURIComponent(this.$route.params.id);
    const encodedNoticeId = encodeURIComponent(this.noticeId);

    const res = await this.$axios({
      method: "GET",
      url: `/api/notices/${encodedNoticeId}`
    });

    res.data.requiredDocuments = res.data.requiredDocuments.map(doc => ({
      ref: doc,
      name: doc
        .replace("_", " ")
        .toLowerCase()
        .capitalize()
    }));
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

        this.$nuxt.$router.replace({ path: "/bidder" });
      } catch (e) {
        console.log(e);
        alert(e.message);
      }
    }
  }
};
</script>