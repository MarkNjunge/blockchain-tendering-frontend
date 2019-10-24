<template>
  <div class="container mx-auto">
    <loading :visible="loading" />
    <div v-if="notices">
      <h2 class="mt-8 section-title">Available Tenders</h2>
      <div class="mt-2 notices">
        <table>
          <thead>
            <tr>
              <th>Notice ID</th>
              <th>Title</th>
              <th>Organization</th>
              <th>Required document(s)</th>
              <th>Deadline</th>
              <th>Status</th>
              <th></th>
              <!-- <th>Opening Venue</th> -->
            </tr>
          </thead>
          <tbody>
            <tr v-for="(notice) in notices" :key="notice.tenderId">
              <td>{{notice.tenderId}}</td>
              <td>{{notice.title}}</td>
              <td>{{notice.organization.name}}</td>
              <td>{{notice.requiredDocuments}}</td>
              <td>{{notice.submissionClosingDate}}</td>
              <td>
                <p class="tag bg-gray-200 text-gray-800">{{notice.status}}</p>
              </td>
              <td>
                <svg
                  class="w-4 h-4 fill-current cursor-pointer hover:text-blue-700"
                  @click="openNotice(notice)"
                >
                  <use href="#open-link" />
                </svg>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
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
      notices: null,
      loading: false
    };
  },
  async mounted() {
    this.loading = true;

    try {
      const res = await this.$axios({
        method: "get",
        url: `/api/notices`
      });

      this.loading = false;
      this.notices = res.data.map(n => {
        n.requiredDocuments = n.requiredDocuments
          .map(d =>
            d
              .replace("_", " ")
              .toLowerCase()
              .capitalize()
          )
          .join(", ");
        return { ...n };
      });
    } catch (e) {
      this.loading = false;
      console.log(e);
      alert(e.message);
    }
  },
  methods: {
    async download(documentRef) {
      try {
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
    openNotice(notice) {
      this.$nuxt.$router.replace({
        path: `/bidder/notices/${encodeURIComponent(notice.tenderId)}`
      });
    }
  }
};
</script>