<template>
  <div class="container mx-auto px-4 xl:w-1/2 md:w-4/5">
    <nuxt-link to="/organization" class="up-navigation">
      <!-- M1 -->
      <svg viewBox="0 0 320 512" class="up-navigation-icon">
        <path
          fill="currentColor"
          d="M34.52 239.03L228.87 44.69c9.37-9.37 24.57-9.37 33.94 0l22.67 22.67c9.36 9.36 9.37 24.52.04 33.9L131.49 256l154.02 154.75c9.34 9.38 9.32 24.54-.04 33.9l-22.67 22.67c-9.37 9.37-24.57 9.37-33.94 0L34.52 272.97c-9.37-9.37-9.37-24.57 0-33.94z"
        />
      </svg>
      <!-- M2 -->
      <!-- <svg class="up-navigation-icon">
        <use href="#back" />
      </svg>-->
      <p class="up-navigation-text">BACK</p>
    </nuxt-link>

    <h1 class="section-title mt-2">Create a Tender Notice</h1>

    <div class="mt-2 mb-8 card">
      <form @submit="submit">
        <div class="input-group">
          <label for="ref" class="input-label">Reference Number</label>
          <input type="text" id="ref" name="ref" class="input-field" v-model="ref" required />
        </div>
        <div class="mt-3 input-group">
          <label for="title" class="input-label">Title</label>
          <input type="text" id="title" name="title" class="input-field" v-model="title" required />
        </div>
        <div class="mt-3 input-group">
          <label for="tenderDocument" class="input-label">Tender Document</label>
          <input type="file" name="card" id="card" class="input-field" @change="onFileSelected" />
        </div>
        <div class="mt-3 input-group">
          <label for="requiredDocuments" class="input-label">Required Documents</label>
          <div class="input-checkbox">
            <input
              type="checkbox"
              name="nhif"
              id="nhif"
              class="input-checkbox-box"
              value="nhif"
              v-model="nhif"
            />
            <label for="nhif" class="input-checkbox-label">NHIF Clearance Certificate</label>
          </div>
          <div class="input-checkbox">
            <input
              type="checkbox"
              name="kra"
              id="kra"
              class="input-checkbox-box"
              value="kra"
              v-model="kra"
            />
            <label for="kra" class="input-checkbox-label">KRA Clearance Certificate</label>
          </div>
          <div class="input-checkbox">
            <input
              type="checkbox"
              name="bidBond"
              id="bidBond"
              value="BidBond"
              v-model="bidBond"
              class="input-checkbox-box"
            />
            <label for="bidBond" class="input-checkbox-label">Bid Bond</label>
          </div>
        </div>
        <div class="mt-3 input-group">
          <label for="extraRequiredDocuments" class="input-label">Extra Required Documents</label>
          <input
            type="text"
            id="extraRequiredDocuments"
            name="extraRequiredDocuments"
            placeholder="Enter a list separated by commas"
            class="input-field"
            v-model="extraRequiredDocuments"
          />
        </div>
        <div class="mt-3 input-group">
          <label for="closingDate" class="input-label">Tender Closing Date</label>
          <input
            type="datetime-local"
            name="closingDate"
            id="closingDate"
            class="input-field"
            v-model="closingDate"
            @change="onClosingDateChange"
            required
          />
        </div>
        <div class="mt-3 input-group">
          <label for="openingDate" class="input-label">Tender Opening Date</label>
          <input
            type="datetime-local"
            name="openingDate"
            id="openingDate"
            class="input-field"
            v-model="openingDate"
            required
          />
        </div>
        <div class="mt-3 input-group">
          <label for="openingVenue" class="input-label">Tender Opening Venue</label>
          <input
            type="text"
            name="openingVenue"
            id="openingVenue"
            class="input-field"
            v-model="openingVenue"
            required
          />
        </div>
        <button type="submit" class="mt-6 w-full btn">Publish</button>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      openingDate: null,
      openingVenue: null,
      closingDate: null,
      nhif: true,
      kra: true,
      bidBond: false,
      extraRequiredDocuments: "",
      title: null,
      ref: null
    };
  },
  methods: {
    onFileSelected(event) {
      this.tenderDocument = event.target.files[0];
    },
    onClosingDateChange() {
      this.openingDate = this.closingDate;
    },
    async submit(e) {
      e.preventDefault();

      if (!this.tenderDocument) {
        return;
      }

      const requiredDocuments = [];
      if (this.nhif) {
        requiredDocuments.unshift("nhif");
      }
      if (this.kra) {
        requiredDocuments.unshift("kra");
      }
      if (this.bidBond) {
        requiredDocuments.unshift("bid bond");
      }
      this.extraRequiredDocuments.split(",").forEach(v => {
        requiredDocuments.push(v.trim());
      });

      const bodyFormData = new FormData();
      bodyFormData.append("id", this.ref);
      bodyFormData.append("document", this.tenderDocument);
      bodyFormData.append("title", this.title);
      bodyFormData.append(
        "requiredDocuments",
        JSON.stringify(requiredDocuments)
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

        this.$nuxt.$router.replace({
          path: `/organization/notices/${encodeURIComponent(this.ref)}`
        });
      } catch (e) {
        console.log(e);
        alert(e.message);
      }
    }
  }
};
</script>