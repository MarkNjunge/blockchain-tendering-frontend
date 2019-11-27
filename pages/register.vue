<template>
  <div class="center-content-form">
    <logo />
    <h2 class="mt-4 section-title">Enter your details to create an account</h2>
    <form @submit="onSubmit" class="mt-2 card" v-if="!downloadToken">
      <div class="input-group">
        <label for="participantType" class="input-label">Participant Type</label>
        <select
          name="participantType"
          id="participantType"
          v-model="participantType"
          class="input-field"
        >
          <option value="TenderingOrganization">Tendering Organization</option>
          <option value="TenderBidder">Tender Bidder</option>
        </select>
      </div>
      <div class="mt-3 input-group">
        <label for="name" class="input-label">Name</label>
        <input type="text" name="name" id="name" v-model="name" class="input-field" required />
      </div>
      <div v-if="participantType == 'TenderBidder'" class="mt-3 input-group">
        <label for="companyRegNo" class="input-label">Company Registration Number</label>
        <input
          type="text"
          name="companyRegNo"
          id="companyRegNo"
          v-model="companyRegNo"
          class="input-field"
          required
        />
      </div>
      <div class="mt-3 input-group">
        <label for="email" class="input-label">Email</label>
        <input type="email" name="email" id="email" v-model="email" class="input-field" required />
      </div>
      <div class="mt-3 input-group">
        <label for="phone" class="input-label">Phone</label>
        <input type="number" name="phone" id="phone" v-model="phone" class="input-field" required />
      </div>
      <div class="mt-3 input-group">
        <label for="streetAddress" class="input-label">Street Address</label>
        <input
          type="text"
          name="streetAddress"
          id="streetAddress"
          v-model="streetAddress"
          class="input-field"
          required
        />
      </div>
      <button type="submit" class="btn mt-6 w-full">Register</button>
    </form>
    <div v-if="downloadToken" class="card flex flex-col items-center">
      <p class="text-3xl font-bold">Registration successfull!</p>
      <p class="mt-4">The download for your authentication card should start automatically.</p>
      <p class>Click here to download again</p>
      <button @click="downloadCard" class="mt-2 btn-secondary inline-block w-fit">Download</button>
      <nuxt-link to="/login">
        <button class="mt-6 btn">Proceed to login</button>
      </nuxt-link>
    </div>
  </div>
</template>

<script>
import Logo from "~/components/Logo.vue";

export default {
  layout: "no-bar",
  components: {
    Logo
  },
  data() {
    return {
      participantType: "TenderingOrganization",
      name: null,
      companyRegNo: null,
      email: null,
      phone: "254712345678",
      streetAddress: null,
      downloadToken: null
    };
  },
  methods: {
    async onSubmit(e) {
      e.preventDefault();

      try {
        const res = await this.$axios({
          method: "post",
          url: "/api/auth/register",
          data: {
            participantType: this.participantType,
            name: this.name,
            companyRegNo: this.companyRegNo,
            email: this.email,
            phone: this.phone,
            streetAddress: this.streetAddress
          }
        });

        this.downloadToken = res.headers["x-card-download-token"];

        window.location = `${
          process.env.apiBaseUrl
        }/files/download?token=${encodeURIComponent(this.downloadToken)}`;
      } catch (e) {
        console.log(e);
        alert(e.message);
      }
    },
    downloadCard() {
      window.location = `${
        process.env.apiBaseUrl
      }/files/download?token=${encodeURIComponent(this.downloadToken)}`;
    }
  }
};
</script>