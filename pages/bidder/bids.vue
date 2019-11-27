<template>
  <div class>
    <div class="center-content" v-if="bids">
      <h2 class="section-title mt-8">Your Bids</h2>
      <table class="mt-2">
        <thead>
          <tr>
            <th>Bid ID</th>
            <th>Notice ID</th>
            <th>Bid Submission Deadline</th>
            <th>Notice Status</th>
            <th>Bid Status</th>
            <th>Bid Posted Date</th>
            <th></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(bid) in bids" :key="bid">
            <td>{{bid.bidId}}</td>
            <td>{{bid.tenderNotice.tenderId}}</td>
            <td>{{bid.tenderNotice.submissionClosingDate}}</td>
            <td>
              <p
                class="tag tag-gray"
                v-if="bid.tenderNotice.status =='PUBLISHED'"
              >{{bid.tenderNotice.status}}</p>
              <p
                class="tag tag-red"
                v-else-if="bid.tenderNotice.status =='CLOSED'"
              >{{bid.tenderNotice.status}}</p>
              <p
                class="tag tag-green"
                v-else-if="bid.tenderNotice.status =='AWARDED'"
              >{{bid.tenderNotice.status}}</p>
              <p
                class="tag tag-orange"
                v-else-if="bid.tenderNotice.status =='WITHDRAWN'"
              >{{bid.tenderNotice.status}}</p>
            </td>
            <td>
              <p class="tag tag-gray" v-if="bid.status =='ACTIVE'">{{bid.status}}</p>
              <p class="tag tag-green" v-else-if="bid.status =='ACCPETED'">{{bid.status}}</p>
              <p class="tag tag-orange" v-else-if="bid.status =='WITHDRAWN'">{{bid.status}}</p>
              <p class="tag tag-red" v-else-if="bid.status =='REJECTED'">{{bid.status}}</p>
            </td>
            <td>{{bid.datePosted}}</td>
            <td>
              <nuxt-link :to="bid.link">
                <svg class="w-4 h-4 fill-current cursor-pointer hover:text-blue-700">
                  <use href="#open-link" />
                </svg>
              </nuxt-link>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <Loading :visible="loading" />
  </div>
</template>

<script>
import Loading from "@/components/Loading";

String.prototype.capitalize = function() {
  return this.replace(/(?:^|\s)\S/g, function(a) {
    return a.toUpperCase();
  });
};

export default {
  components: {
    Loading
  },
  data() {
    return {
      loading: false,
      bids: null
    };
  },
  async mounted() {
    this.loading = true;
    const res = await this.$axios({
      method: "GET",
      url: "/api/bids/forCurrent"
    });

    const options = { timeStyle: "short", dateStyle: "long" };

    this.bids = res.data.map(b => {
      b.tenderNotice.submissionClosingDate = new Date(
        b.tenderNotice.submissionClosingDate
      ).toLocaleDateString("en-US", options);

      b.datePosted = new Date(b.datePosted).toLocaleDateString(
        "en-US",
        options
      );

      b.link = `/bidder/notices/${encodeURIComponent(b.tenderNotice.tenderId)}`;

      return { ...b };
    });
    this.loading = false;
  }
};
</script>