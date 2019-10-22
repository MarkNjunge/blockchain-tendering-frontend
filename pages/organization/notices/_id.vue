<template>
  <div>
    <div class="pt-4 container mx-auto" v-if="!notice">
      <p>Loading...</p>
    </div>
    <div v-if="notice">
      <div class="bg-gray-200">
        <div class="pt-4 pb-4 container mx-auto">
          <nuxt-link to="/organization" class="up-navigation">
            <svg class="up-navigation-icon">
              <use href="#back" />
            </svg>
            <p class="up-navigation-text">BACK</p>
          </nuxt-link>
          <div class="mt-2 flex items-center">
            <h2 class="text-2xl">{{notice.title}}</h2>
            <p
              class="tag ml-2 bg-green-200 text-green-700"
              v-if="notice.status =='PUBLISHED'"
            >{{notice.status}}</p>

            <p
              class="tag ml-2 bg-gray-400 text-gray-700"
              v-if="notice.status =='AWARDED'"
            >{{notice.status}}</p>
          </div>
          <p class="px-2 bg-gray-300 rounded text-xs inline-block">{{notice.tenderId}}</p>
          <div class="mt-2">
            <h3 class="text-gray-700 text-sm">Tender Document</h3>
            <div
              class="flex w-fit cursor-pointer border border-blue-700 px-2 py-1 rounded hover:bg-blue-100 text-blue-700"
              @click="downloadDocument()"
            >
              <svg class="h-6 w-6 fill-current">
                <use href="#download-document" />
              </svg>
              <p class="ml-2">Download</p>
            </div>
            <p class="mt-1">SHA256 hash: {{notice.tenderDocument.documentHash}}</p>
          </div>
          <div class="mt-2">
            <h3 class="text-gray-700 text-sm">Submission Details</h3>
            <p>Required Documents: {{notice.requiredDocumentsString}}</p>
            <p>Submission Deadline: {{notice.submissionClosingDate}}</p>
            <p>Opening Date: {{notice.openingDate}}</p>
            <p>Opening Venue: {{notice.openingVenue}}</p>
          </div>
        </div>
      </div>
      <div class="pt-4 container mx-auto" v-if="bids">
        <h2 class="text-xl text-gray-700">Bids</h2>
        <table class="mt-2 text-left w-full border-collapse card">
          <thead>
            <tr>
              <th
                class="py-4 px-6 bg-gray-200 font-bold uppercase text-sm text-gray-dark border-b border-gray-500"
              >Bid Id</th>
              <th
                class="py-4 px-6 bg-gray-200 font-bold uppercase text-sm text-gray-dark border-b border-gray-500"
              >Bidder</th>
              <th
                class="py-4 px-6 bg-gray-200 font-bold uppercase text-sm text-gray-dark border-b border-gray-500"
              >Status</th>
              <th
                class="py-4 px-6 bg-gray-200 font-bold uppercase text-sm text-gray-dark border-b border-gray-500"
              >Document</th>
              <th
                v-for="(doc, index) in notice.requiredDocuments"
                :key="index"
                class="py-4 px-6 bg-gray-200 font-bold uppercase text-sm text-gray-dark border-b border-gray-500"
              >{{doc}}</th>
              <th
                class="py-4 px-6 bg-gray-200 font-bold uppercase text-sm text-gray-dark border-b border-gray-500"
              >Actions</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(bid) in bids" :key="bid.bidId" class="hover:bg-gray-100">
              <td>{{bid.bidId}}</td>
              <td>{{bid.bidder.name}}</td>
              <td>
                <p class="tag bg-gray-300 text-gray-700" v-if="bid.status == 'ACTIVE'">Active</p>
                <p class="tag bg-green-200 text-green-700" v-if="bid.status == 'ACCEPTED'">Accepted</p>
                <p
                  class="tag bg-orange-200 text-orange-700"
                  v-if="bid.status == 'WITHDRAWN'"
                >Withdrawn</p>
                <p class="tag bg-red-200 text-red-700" v-if="bid.status == 'REJECTED'">Rejected</p>
              </td>
              <td>
                <p class="table-btn" @click="downloadFile(bid.bidDocument.documentRef)">Download</p>
              </td>
              <td v-for="(doc, index) in bid.requiredDocuments" :key="index">
                <p class="table-btn" @click="downloadFile(doc.documentRef)">Download</p>
              </td>
              <td class="flex justify-between">
                <p
                  class="px-2 text-white bg-green-700 w-fit h-fit rounded cursor-pointer hover:bg-green-600"
                  v-if="canBeWinner(bid)"
                  @click="selectWinningBid(bid.bidId)"
                >Winner</p>
                <p
                  class="px-2 text-white bg-red-700 w-fit h-fit rounded cursor-pointer hover:bg-red-600"
                  v-if="canBeRejected(bid)"
                  @click="rejectBid(bid.bidId)"
                >Reject</p>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
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
      notice: null,
      bids: null,
      tenderId: null,
      rejectionReason: null,
      rejectionNarrative: null
    };
  },
  computed: {},
  async created() {
    this.tenderId = decodeURIComponent(this.$route.params.id);
    const encodedTenderId = encodeURIComponent(this.tenderId);

    try {
      let res = await this.$axios({
        method: "get",
        url: `/api/notices/${encodedTenderId}`
      });

      const options = { timeStyle: "short", dateStyle: "long" };

      res.data.submissionClosingDate = new Date(
        res.data.submissionClosingDate
      ).toLocaleDateString("en-US", options);
      res.data.openingDate = new Date(res.data.openingDate).toLocaleDateString(
        "en-US",
        options
      );

      res.data.requiredDocuments = res.data.requiredDocuments.map(doc => {
        return doc
          .replace("_", " ")
          .toLowerCase()
          .capitalize();
      });

      res.data.requiredDocumentsString = res.data.requiredDocuments.join(", ");

      this.notice = res.data;

      res = await this.$axios({
        method: "get",
        url: `/api/bids?noticeId=${encodedTenderId}`
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
        const encodedTenderId = encodeURIComponent(this.tenderId);

        const res = await this.$axios({
          method: "post",
          url: `/api/notices/${encodedTenderId}/result`,
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
      console.log(`Rejecting ${bidId}`);
      // TODO Reject bid
      // try {
      //   const res = await this.$axios({
      //     method: "post",
      //     url: `/api/bids/${encodeURIComponent(bidId)}/reject`,
      //     data: {
      //       reason: this.rejectionReason,
      //       reasonNarrative: this.rejectionNarrative
      //     }
      //   });

      //   alert("The bid has been rejected.");
      // } catch (e) {
      //   console.log(e);
      //   alert(e.response.data.message || e.message);
      // }
    },
    async downloadDocument() {
      try {
        const documentRef = this.notice.tenderDocument.documentRef;
        const res = await this.$axios({
          method: "get",
          url: `/api/files/documents?ref=${encodeURIComponent(documentRef)}`
        });

        window.location = `${
          process.env.apiBaseUrl
        }/files/documents?ref=${encodeURIComponent(documentRef)}`;
      } catch (e) {
        console.log(e);
        alert(e.message);
      }
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
    canBeWinner(bid) {
      // Should be computed function
      if (this.notice.status == "AWARDED") {
        return false;
      } else if (bid.status == "ACCEPTED" || bid.status == "WITHDRAWN") {
        return false;
      } else {
        return true;
      }
    },
    canBeRejected(bid) {
      if (bid.status == "ACCEPTED" || bid.status == "WITHDRAWN") {
        return false;
      } else {
        return true;
      }
    }
  }
};
</script>
