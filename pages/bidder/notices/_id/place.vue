<template>
  <div class="mt-8 center-content-form">
    <div to="/bidder" class="up-navigation" @click="back">
      <svg class="up-navigation-icon">
        <use href="#back" />
      </svg>
      <p class="up-navigation-text">BACK</p>
    </div>
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
        <textarea
          type="text"
          name="bidSummary"
          id="bidSummary"
          v-model="bidSummary"
          class="input-field"
          rows="4"
        />
        <span class="input-helper">The introduction of your 'Form of Tender'</span>
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
  async mounted() {
    this.noticeId = decodeURIComponent(this.$route.params.id);
    const encodedNoticeId = encodeURIComponent(this.noticeId);

    const res = await this.$axios({
      method: "GET",
      url: `/api/notices/${encodedNoticeId}`
    });

    res.data.requiredDocuments = res.data.requiredDocuments.map(doc => ({
      ref: doc,
      name: doc
        .replace(/_/g, " ")
        .toLowerCase()
        .capitalize()
    }));
    this.tenderNotice = res.data;
  },
  methods: {
    back() {
      window.history.back();
    },
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

        this.$nuxt.$router.push({
          path: `/bidder/notices/${encodeURIComponent(
            this.tenderNotice.tenderId
          )}`
        });
      } catch (e) {
        console.log(e);
        alert(e.message);
      }
    }
  }
};
</script>