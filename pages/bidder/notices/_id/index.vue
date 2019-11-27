<template>
  <div class>
    <div class v-if="notice">
      <div class="pt-4 pb-4 bg-gray-200">
        <div class="center-content">
          <!-- <div class="container mx-auto px-4 xl:w-1/2 md:w-4/5"> -->
          <div class="flex justify-between items-end">
            <div class="w-full">
              <h2 class="mt-2 text-xl">{{notice.title}}</h2>
              <p class="px-2 bg-gray-300 rounded text-xs inline-block">{{notice.tenderId}}</p>
              <div class="mt-2">
                <p class="tag tag-gray" v-if="notice.status =='PUBLISHED'">{{notice.status}}</p>
                <p class="tag tag-red" v-else-if="notice.status =='CLOSED'">{{notice.status}}</p>
                <p class="tag tag-green" v-else-if="notice.status =='AWARDED'">{{notice.status}}</p>
                <p class="tag tag-orange" v-else-if="notice.status =='WITHDRAWN'">{{notice.status}}</p>
              </div>
              <div class="mt-4 flex justify-between">
                <div class>
                  <p class="text-gray-600 text-sm">Date Published</p>
                  <p>{{notice.datePublished}}</p>
                </div>
                <div class>
                  <p class="text-gray-600 text-sm">Submission Deadline</p>
                  <p>{{notice.submissionClosingDate}}</p>
                </div>
                <div class>
                  <p class="text-gray-600 text-sm">Opening Date</p>
                  <p>{{notice.openingDate}}</p>
                </div>
              </div>
              <div class="mt-2">
                <p class="text-gray-600 text-sm">Opening Venue</p>
                <p>{{notice.openingVenue}}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="center-content-form mt-4" v-if="bid">
      <h2 class="section-title">Your bid</h2>
      <div class="mt-2 card-p-6">
        <div class="flex items-baseline">
          <p class="mt-2 font-bold">{{bid.bidId}}</p>
          <div class="ml-2">
            <p class="tag tag-gray" v-if="bid.status == 'ACTIVE'">{{bid.status}}</p>
            <p class="tag tag-green" v-else-if="bid.status == 'ACCEPTED'">{{bid.status}}</p>
            <p class="tag tag-orange" v-else-if="bid.status == 'WITHDRAWN'">{{bid.status}}</p>
            <p class="tag tag-red" v-else-if="bid.status == 'REJECTED'">{{bid.status}}</p>
          </div>
        </div>
        <p class="mt-1 text-gray-700">{{bid.summary}}</p>
        <div class="flex mt-4">
          <div
            class="flex items-center w-fit cursor-pointer py-1 px-2 rounded border border-blue-700 bg-blue-100 text-blue-800 hover:bg-blue-700 hover:text-white"
            @click="downloadFile(bid.bidDocument.documentRef)"
          >
            <svg class="w-4 h-4 fill-current">
              <use href="#download-document" />
            </svg>
            <button class="ml-2">Bid document</button>
          </div>
          <div
            class="ml-2 flex items-center w-fit cursor-pointer py-1 px-2 rounded border border-blue-700 bg-blue-100 text-blue-800 hover:bg-blue-700 hover:text-white"
            v-for="(doc, index) in bid.requiredDocuments"
            :key="index"
            @click="downloadFile(doc.documentRef)"
          >
            <svg class="w-4 h-4 fill-current">
              <use href="#download-document" />
            </svg>
            <button class="ml-2">{{doc.key}}</button>
          </div>
        </div>
        <div class="flex justify-between items-baseline">
          <p class="mt-2 text-gray-600">{{bid.datePosted}}</p>

          <button
            class="btn-withdraw"
            @click="withdrawBid"
            v-if="bidCanBeWithdrawn(bid.status)"
          >Withdraw</button>
        </div>
      </div>
    </div>
    <div class="mt-8 center-content-form" v-if="bid == false">
      <nuxt-link :to="'/bidder/notices/' + encodeURIComponent(this.noticeId) + '/place'">
        <div
          class="flex flex-col items-center justify-center cursor-pointer text-gray-700 hover:text-gray-800 p-6 bg-white rounded border border-blue-700 shadow inline-block"
        >
          <h2 class="text-lg">No bid placed</h2>
          <h3 class="text-sm">Place a bid</h3>
          <svg class="mt-2 w-6 h-6 fill-current">
            <use href="#add" />
          </svg>
        </div>
      </nuxt-link>
    </div>

    <loading :visible="loading" />
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
      noticeId: null,
      notice: null,
      bid: null
    };
  },
  async created() {
    this.noticeId = decodeURIComponent(this.$route.params.id);
    const encodedNoticeId = encodeURIComponent(this.noticeId);
    const options = { timeStyle: "short", dateStyle: "long" };

    try {
      this.loading = true;
      const res = await this.$axios({
        method: "get",
        url: `/api/notices/${encodedNoticeId}`
      });

      res.data.submissionClosingDate = new Date(
        res.data.submissionClosingDate
      ).toLocaleDateString("en-US", options);

      res.data.datePublished = new Date(
        res.data.datePublished
      ).toLocaleDateString("en-US", options);
      res.data.openingDate = new Date(res.data.openingDate).toLocaleDateString(
        "en-US",
        options
      );

      this.notice = res.data;
    } catch (e) {
      this.loading = false;
      console.log(e);
      alert(e.message);
    }

    try {
      const res = await this.$axios({
        method: "get",
        url: `/api/bids/forCurrent/${encodedNoticeId}`
      });

      res.data.requiredDocuments = res.data.requiredDocuments.map(doc => {
        const n = doc.key
          .replace(/_/g, " ")
          .toLowerCase()
          .capitalize();

        doc.key = n;
        console.log();
        return doc;
      });

      res.data.datePosted = new Date(res.data.datePosted).toLocaleDateString(
        "en-US",
        options
      );

      this.bid = res.data;
      this.loading = false;
    } catch (e) {
      this.loading = false;

      if (e.response.status == 404) {
        this.bid = false;
      } else {
        console.log(e);
        alert(e.message);
      }
    }
  },
  methods: {
    async withdrawBid() {
      this.loading = true;
      const res = await this.$axios({
        method: "delete",
        url: `/api/bids/${encodeURIComponent(this.bid.bidId)}`
      });
      this.loading = false;

      alert("Your bid has been withdrawn");
    },
    async downloadFile(ref) {
      try {
        const res = await this.$axios({
          method: "get",
          url: `/api/files/documents?ref=${encodeURIComponent(ref)}`
        });
        window.location = `${
          process.env.apiBaseUrl
        }/files/documents?ref=${encodeURIComponent(ref)}`;
      } catch (e) {
        console.log(e);
        alert(e.message);
      }
    },
    bidCanBeWithdrawn(status) {
      if (
        status == "ACCEPTED" ||
        status == "WITHDRAWN" ||
        status == "REJECTED"
      ) {
        return false;
      } else {
        return true;
      }
    }
  }
};
</script>