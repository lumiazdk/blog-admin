<template>
  <div>
    <markdown :content="data.content"></markdown>
  </div>
</template>
<script>
import axios from "axios";
import Vue from "vue";
import markdown from "../components/markdown";

export default {
  components: {
    markdown
  },
  data() {
    return {
      data: {
        content: "# 请稍等"
      }
    };
  },
  methods: {
    getDetail() {
      axios({
        method: "post",
        url: "/getPost",
        data: {
          page: 1,
          where: {
            id: this.$route.query.id
          }
        }
      }).then(res => {
        let data = res.data.result.data.rows[0];
        this.data = data;
      });
    }
  },
  created() {
    this.getDetail();
  },
 
};
</script>
<style scoped>
</style>
