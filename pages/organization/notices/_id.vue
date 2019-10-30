<template>
  <div>
    <div class="pt-4 container mx-auto" v-if="!notice">
      <p>Loading...</p>
    </div>
    <div id="myModal" class="modal" v-if="rejection">
      <div class="modal-content card container mx-auto px-4 xl:w-1/2 md:w-4/5">
        <div class @click="closeRejectBidUI">Close</div>
        <div class="section-title">Reject Tender Bid</div>
        <div class="input-group">
          <label for="bidId" class="input-label">Tender Bid ID</label>
          <input
            type="text"
            name="bidId"
            id="bidId"
            class="input-field-disabled"
            v-model="rejection.bidId"
            disabled
          />
        </div>
        <div class="mt-3 input-group">
          <label for="rejectionReason" class="input-label">Rejection reason</label>
          <select
            class="input-field"
            name="rejectionReason"
            id="rejectionReason"
            v-model="rejection.rejectionReason"
            required
          >
            <option value="UNRESPONSIVE">Unresponsive (Does not meet requirements for tender)</option>
            <option value="OTHER">Other</option>
          </select>
        </div>
        <div class="mt-3 input-group">
          <label for="rejectionReasonNarrative" class="input-label">Reason narrative</label>
          <input
            type="textarea"
            name="rejectionReasonNarrative"
            id="rejectionReasonNarrative"
            class="input-field"
            v-model="rejection.rejectionReasonNarrative"
            required
          />
        </div>
        <button class="mt-6 w-full btn" @click="rejectBid">Reject</button>
      </div>
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
            <div
              class="flex items-center w-fit cursor-pointer py-1 px-1 rounded border border-blue-700 bg-blue-100 text-blue-800 hover:bg-blue-700 hover:text-white"
              @click="downloadDocument()"
            >
              <svg class="h-6 w-6 fill-current">
                <use href="#download-document" />
              </svg>
              <p class="ml-2">Tender Document</p>
            </div>
            <p class="mt-1 text-xs text-gray-700">SHA-256: {{notice.tenderDocument.documentHash}}</p>
          </div>
          <div class="mt-2 flex flex-wrap justify-between">
            <div class="mt-2">
              <p class="text-gray-600 text-sm">Required Documents</p>
              <p>{{notice.requiredDocumentsString}}</p>
            </div>
            <div class="mt-2">
              <p class="text-gray-600 text-sm">Submission Closing Date</p>
              <p>{{notice.submissionClosingDate}}</p>
            </div>
            <div class="mt-2">
              <p class="text-gray-600 text-sm">Opening Date</p>
              <p>{{notice.openingDate}}</p>
            </div>
            <div class="mt-2">
              <p class="text-gray-600 text-sm">Opening Venue</p>
              <p>{{notice.openingVenue}}</p>
            </div>
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
                <button
                  class="table-btn"
                  @click="downloadFile(bid.bidDocument.documentRef)"
                >Download</button>
              </td>
              <td v-for="(doc, index) in bid.requiredDocuments" :key="index">
                <button class="table-btn" @click="downloadFile(doc.documentRef)">Download</button>
              </td>
              <td>
                <div class="flex">
                  <svg
                    class="mr-2 p-1 w-8 h-8 rounded bg-green-200 text-green-800 hover:bg-green-600 hover:text-white fill-current cursor-pointer"
                    v-if="canBeWinner(bid)"
                    @click="selectWinningBid(bid.bidId)"
                  >
                    <use href="#vote-yea" />
                  </svg>
                  <svg
                    title="Reject bid"
                    class="mr-2 p-1 w-8 h-8 rounded bg-red-200 text-red-800 hover:bg-red-600 hover:text-white fill-current cursor-pointer"
                    v-if="canBeRejected(bid)"
                    @click="showRejectBidUI(bid.bidId)"
                  >
                    <use href="#vote-nay" />
                  </svg>
                </div>
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
      rejection: null
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
    async showRejectBidUI(bidId) {
      this.rejection = {
        bidId,
        rejectionReason: null,
        rejectionReasonNarrative: null
      };
    },
    async closeRejectBidUI() {
      this.rejection = null;
    },
    async rejectBid() {
      console.log(this.rejection);

      try {
        const res = await this.$axios({
          method: "post",
          url: `/api/bids/${encodeURIComponent(this.rejection.bidId)}/reject`,
          data: {
            reason: this.rejection.rejectionReason,
            reasonNarrative: this.rejection.rejectionReasonNarrative
          }
        });
        alert("The bid has been rejected.");
      } catch (e) {
        console.log(e);
        alert(e.response.data.message || e.message);
      }
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
      if (
        bid.status == "ACCEPTED" ||
        bid.status == "WITHDRAWN" ||
        bid.status == "REJECTED"
      ) {
        return false;
      } else {
        return true;
      }
    }
  }
};
</script>

<style lang="css">
.modal {
  position: fixed; /* Stay in place */
  z-index: 1; /* Sit on top */
  left: 0;
  top: 0;
  width: 100%; /* Full width */
  height: 100%; /* Full height */
  overflow: auto; /* Enable scroll if needed */
  background-color: rgb(0, 0, 0); /* Fallback color */
  background-color: rgba(0, 0, 0, 0.4); /* Black w/ opacity */
}

/* Modal Content/Box */
.modal-content {
  background-color: #fefefe;
  margin: 15% auto; /* 15% from the top and centered */
  padding: 20px;
  border: 1px solid #888;
  /* width: 80%; Could be more or less, depending on screen size */
}

/* The Close Button */
.close {
  color: #aaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
}

.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}
</style>