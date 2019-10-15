<template>
  <div>
    <form @submit="onSubmit">
      <div class>
        <label for="participantType">Participant Type</label>
        <select name="participantType" id="participantType" v-model="participantType">
          <option value="TenderingOrganization">Tendering Organization</option>
          <option value="TenderBidder">Tender Bidder</option>
        </select>
      </div>
      <div class>
        <label for="name">Name</label>
        <input type="text" name="name" id="name" v-model="name" required />
      </div>
      <div class v-if="participantType == 'TenderBidder'">
        <label for="companyRegNo">Company Registration Number</label>
        <input type="text" name="companyRegNo" id="companyRegNo" v-model="companyRegNo" required />
      </div>
      <div class>
        <label for="email">Email</label>
        <input type="email" name="email" id="email" v-model="email" required />
      </div>
      <div class>
        <label for="phone">Phone</label>
        <input type="number" name="phone" id="phone" v-model="phone" required />
      </div>
      <div class>
        <label for="streetAddress">Street Address</label>
        <input type="text" name="streetAddress" id="streetAddress" v-model="streetAddress" required />
      </div>
      <button type="submit">Register</button>
    </form>
    <div v-if="downloadToken">
      <p>Registration successfull!</p>
      <p>The download for your authentication card should start automatically.</p>
      <p>Click here to download again</p>
      <button @click="downloadCard">Download</button>
    </div>
  </div>
</template>

<script>
export default {
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

        window.location = `http://localhost:3000/files/download?token=${encodeURIComponent(
          this.downloadToken
        )}`;
      } catch (e) {
        console.log(e);
        alert(e.message);
      }
    },
    downloadCard() {
      window.location = `http://localhost:3000/files/download?token=${encodeURIComponent(
        this.downloadToken
      )}`;
    }
  }
};
</script>