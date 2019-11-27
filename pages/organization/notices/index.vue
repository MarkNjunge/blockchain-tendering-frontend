<template>
  <div class="center-content">
    <h1 class="mt-4 section-title">Notices</h1>
    <table class="mt-2">
      <thead>
        <tr>
          <th>Notice ID</th>
          <th>Title</th>
          <th>Required document(s)</th>
          <th>Deadline</th>
          <th>Status</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(notice) in notices" :key="notice.tenderId">
          <td>{{notice.tenderId}}</td>
          <td>{{notice.title}}</td>
          <td>{{notice.requiredDocuments}}</td>
          <td>{{notice.submissionClosingDate}}</td>
          <td>
            <p class="tag bg-gray-200 text-gray-800">{{notice.status}}</p>
          </td>
          <td>
            <nuxt-link :to="notice.link">
              <svg class="w-4 h-4 fill-current cursor-pointer hover:text-blue-700">
                <use href="#open-link" />
              </svg>
            </nuxt-link>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
String.prototype.capitalize = function() {
  return this.replace(/(?:^|\s)\S/g, function(a) {
    return a.toUpperCase();
  });
};
const options = { timeStyle: "short", dateStyle: "long" };

export default {
  data() {
    return {
      notices: "Loading..."
    };
  },
  async mounted() {
    const res = await this.$axios({
      method: "get",
      url: "/api/notices/forCurrent"
    });

    this.notices = res.data.map(n => {
      n.requiredDocuments = n.requiredDocuments.length;
      n.submissionClosingDate = new Date(
        n.submissionClosingDate
      ).toLocaleDateString("en-US", options);

      n.link = `/organization/notices/${encodeURIComponent(n.tenderId)}`;
      return { ...n };
    });
  }
};
</script>