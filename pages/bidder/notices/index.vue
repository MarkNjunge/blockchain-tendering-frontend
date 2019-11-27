<template>
  <div class="center-content">
    <div v-if="notices">
      <h2 class="mt-8 section-title">Available Tenders</h2>
      <div class="mt-2 notices">
        <table>
          <thead>
            <tr>
              <th>Notice ID</th>
              <th>Title</th>
              <th>Organization</th>
              <th>Deadline</th>
              <th>Status</th>
              <th></th>
              <!-- <th>Opening Venue</th> -->
            </tr>
          </thead>
          <tbody>
            <tr v-for="(notice) in notices" :key="notice.tenderId">
              <td>{{notice.tenderIdDisplay}}</td>
              <td>{{notice.title}}</td>
              <td>{{notice.organization.name}}</td>
              <td>{{notice.submissionClosingDate}}</td>
              <td>
                <p class="tag tag-gray" v-if="notice.status =='PUBLISHED'">{{notice.status}}</p>
                <p class="tag tag-red" v-else-if="notice.status =='CLOSED'">{{notice.status}}</p>
                <p class="tag tag-green" v-else-if="notice.status =='AWARDED'">{{notice.status}}</p>
                <p class="tag tag-orange" v-else-if="notice.status =='WITHDRAWN'">{{notice.status}}</p>
              </td>
              <td>
                <nuxt-link :to="'/bidder/notices/' + encodeURIComponent(notice.tenderId)">
                  <svg class="w-4 h-4 fill-current cursor-pointer hover:text-blue-700">
                    <use href="#open-link" />
                  </svg>
                </nuxt-link>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
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

function truncate(text, limit) {
  return (
    text
      .split(" ")
      .slice(0, limit)
      .join(" ") + "..."
  );
}

function truncateChars(text, limit) {
  return (
    text
      .split("")
      .slice(0, limit)
      .join("") + "..."
  );
}

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

      const options = { dateStyle: "long" };
      // const options = { timeStyle: "short", dateStyle: "long" };

      this.loading = false;
      this.notices = res.data.map(n => {
        n.requiredDocuments = n.requiredDocuments.length;
        n.title = truncate(n.title, 6);
        n.organization.name = truncate(n.organization.name, 3);
        n.tenderIdDisplay = truncateChars(n.tenderId, 10);

        n.submissionClosingDate = new Date(
          n.submissionClosingDate
        ).toLocaleDateString("en-US", options);
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
    }
  }
};
</script>