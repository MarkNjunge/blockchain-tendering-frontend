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
  async asyncData({ $axios }) {
    try {
      const res = await $axios({
        method: "get",
        url: `/api/notices`
      });

      return {
        notices: res.data
      };
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

        window.location = `http://localhost:3000/files/documents?ref=${encodeURIComponent(
          documentRef
        )}`;

        // Try to find out the filename from the content disposition `filename` value
        //  =======================================
        // const disposition = res.headers["content-disposition"];
        // const matches = /"([^"]*)"/.exec(disposition);
        // const filename = matches != null && matches[1] ? matches[1] : "file.pdf";

        // // The actual download
        // const blob = new Blob([res.data], { type: "application/pdf" });
        // const link = document.createElement("a");
        // link.href = window.URL.createObjectURL(blob);
        // link.download = filename;

        // document.body.appendChild(link);

        // link.click();

        // document.body.removeChild(link);
        //  =======================================
      } catch (e) {
        console.log(e);
        alert(e.message);
      }
    }
  }
};
</script>