<template>
  <div>
    <h1>ALL NOTICES</h1>
    <br />
    <div v-for="(notice) in notices" :key="notice.tenderId">
      {{notice}}
      <button @click="download(notice.tenderDocument.documentRef)">Download</button>
      <br />
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      notices: null
    };
  },
  async mounted() {
    try {
      const res = await this.$axios({
        method: "get",
        url: `/api/notices`
      });

      this.notices = res.data;
    } catch (e) {
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