<template>
  <div>
    <!-- Recent bids -->
    <div v-if="bids" class="center-content mt-8">
      <h2 class="section-title">Recent bids</h2>
      <div class="recent-bids mt-2 mb-2">
        <BidCard v-for="(bid, index) in bids" :key="index" :bid="bid" />
      </div>
      <nuxt-link to="/bidder/bids" class="font-bold text-blue-800 hover:text-blue-600">See all</nuxt-link>
    </div>
    <div class="center-content mt-6" v-if="notices">
      <h2 class="section-title">Recent Notices</h2>
      <div class="recent-notices mt-2 mb-2">
        <BidderNoticeCard v-for="(notice, index) in notices" :key="index" :notice="notice" />
      </div>
      <nuxt-link to="/bidder/notices" class="font-bold text-blue-800 hover:text-blue-600">See all</nuxt-link>
    </div>
    <Loading :visible="loading" />
  </div>
</template>

<script>
import BidCard from "@/components/BidCard";
import BidderNoticeCard from "@/components/BidderNoticeCard";
import Loading from "@/components/Loading";

export default {
  components: {
    BidCard,
    Loading,
    BidderNoticeCard
  },
  data() {
    return {
      loading: false,
      bids: null,
      notices: null
    };
  },
  async created() {
    this.loading = true;
    let res = await this.$axios({
      method: "GET",
      url: "/api/bids/forCurrent"
    });

    this.bids = res.data.slice(0, 3).map(n => {
      const d = new Date(n.datePosted);
      const options = { dateStyle: "long" };
      n.datePosted = d.toLocaleDateString("en-US", options);
      return { ...n };
    });

    res = await this.$axios({
      method: "GET",
      url: "/api/notices"
    });
    this.notices = res.data.slice(0, 3).map(n => {
      const d = new Date(n.submissionClosingDate);
      const options = { dateStyle: "long" };
      n.submissionClosingDate = d.toLocaleDateString("en-US", options);
      return { ...n };
    });

    this.loading = false;
  },
  methods: {
    async logout() {
      const res = await this.$axios({
        method: "post",
        url: "/api/auth/logout"
      });

      window.localStorage.removeItem("session");
      window.localStorage.removeItem("profile");
      this.$nuxt.$router.replace({ path: "/login" });
    },
    async withdraw(bid) {
      console.log(`Withdraw: ${bid.bidId}`);
    }
  }
};
</script>

<style lang="scss">
.recent-bids,
.recent-notices {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: 1rem;
}
</style>