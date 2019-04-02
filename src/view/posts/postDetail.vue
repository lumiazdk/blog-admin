<template>
  <div v-highlight v-html="compiledMarkdown" class="markdown-body"></div>
</template>
<script>
import axios from "axios";
import marked from "marked";
import hljs from "highlight.js";
import Vue from "vue";
Vue.directive("highlight", el => {
  let blocks = el.querySelectorAll("pre code");
  blocks.forEach(block => {
    hljs.highlightBlock(block);
  });
});
export default {
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
  computed: {
    compiledMarkdown() {
      //this.articleDetail.context数据
      return marked(this.data.content, { sanitize: true });
    }
  }
};
</script>
<style scoped>
</style>
