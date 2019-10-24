<template>
  <div class>
    <div class v-if="notice">
      <div class="pt-4 pb-4 bg-gray-200">
        <div class="container mx-auto px-4 xl:w-1/2 md:w-4/5">
          <div class="flex justify-between items-end">
            <div class>
              <div class="mt-2 flex items-center">
                <h2 class="text-2xl">{{notice.title}}</h2>
                <p
                  class="tag ml-2 bg-gray-400 text-gray-700"
                  v-if="notice.status =='PUBLISHED'"
                >{{notice.status}}</p>
                <p
                  class="tag ml-2 bg-green-200 text-green-700"
                  v-if="notice.status =='AWARDED'"
                >{{notice.status}}</p>
              </div>
              <p class="px-2 bg-gray-300 rounded text-xs inline-block">{{notice.tenderId}}</p>
            </div>
            <button class="btn h-fit" @click="placeBid">Place bid</button>
          </div>
        </div>
      </div>
    </div>
    <loading :visible="loading" />
  </div>
</template>

<script>
import Loading from "@/components/Loading";

export default {
  components: {
    Loading
  },
  data() {
    return {
      loading: false,
      noticeId: null,
      notice: null
    };
  },
  async created() {
    this.noticeId = decodeURIComponent(this.$route.params.id);
    const encodedNoticeId = encodeURIComponent(this.noticeId);

    try {
      this.loading = true;
      const res = await this.$axios({
        method: "get",
        url: `/api/notices/${encodedNoticeId}`
      });

      this.loading = false;
      this.notice = res.data;
    } catch (e) {
      this.loading = false;
      console.log(e);
      alert(e.message);
    }
  },
  methods: {
    placeBid() {
      this.$nuxt.$router.push({
        path: `/bidder/notices/${encodeURIComponent(this.noticeId)}/place`
      });
    }
  }
};
</script>