<template>
  <div>
    <h1>Notices</h1>
    <p>{{notices}}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      notices: "Loading..."
    };
  },
  async asyncData({ $axios }) {
    const res = await $axios.$get("/api/notices/forCurrent");

    return {
      notices: res
    };
  }
};
</script>