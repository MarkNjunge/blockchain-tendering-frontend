<template>
  <div>
    <h1>NOTICE BY ID</h1>
    <br />
    {{notice}}
    <br />
    <h1>BIDS</h1>
    <br />
    <div class v-for="(bid, index) in bids" :key="bid.bidId">
      <p>{{index + 1}}: {{bid}}</p>
      <button @click="selectWinningBid(bid.bidId)">Select as winner</button>
      <br />
      <select name="rejectionReason" id="rejectionReason" v-model="rejectionReason">
        <option value="UNRESPONSIVE">Unresponsive (Does not meet requirements for tender)</option>
        <option value="OTHER">Other</option>
      </select>
      <div class>
        <label for="rejectionNarrative">Rejection explanation</label>
        <input
          type="text"
          name="rejectionNarrative"
          id="rejectionNarrative"
          v-model="rejectionNarrative"
        />
      </div>
      <button @click="rejectBid(bid.bidId)">Reject</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      notice: null,
      bids: null,
      tenderId: null,
      rejectionReason: null,
      rejectionNarrative: null
    };
  },
  async created() {
    this.tenderId = this.$route.params.id;

    try {
      let res = await this.$axios({
        method: "get",
        url: `/api/notices/${this.tenderId}`
      });

      this.notice = res.data;

      res = await this.$axios({
        method: "get",
        url: `/api/bids?noticeId=${this.tenderId}`
      });

      this.bids = res.data;
    } catch (e) {
      console.log(e);
      alert(e.message);
    }
  },
  methods: {
    async selectWinningBid(bidId) {
      try {
        const res = await this.$axios({
          method: "post",
          url: `/api/notices/${this.tenderId}/result`,
          data: {
            winningBid: bidId
          }
        });

        alert("The bid has been selected as the winner.");
      } catch (e) {
        console.log(e);
        alert(e.response.data.message || e.message);
      }
    },
    async rejectBid(bidId) {
      try {
        const res = await this.$axios({
          method: "post",
          url: `/api/bids/${encodeURIComponent(bidId)}/reject`,
          data: {
            reason: this.rejectionReason,
            reasonNarrative: this.rejectionNarrative
          }
        });

        alert("The bid has been rejected.");
      } catch (e) {
        console.log(e);
        alert(e.response.data.message || e.message);
      }
    }
  }
};
</script>