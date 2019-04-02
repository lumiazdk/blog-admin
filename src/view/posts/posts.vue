<template>
  <Card>
    <p slot="title">文章</p>
    <a href="#" slot="extra" @click.prevent="editPostTap">
      <Icon type="ios-loop-strong"></Icon>添加
    </a>
    <el-table :data="tableData" style="width: 100%">
      <el-table-column prop="id" label="ID"></el-table-column>
      <el-table-column prop="title" label="标题"></el-table-column>
      <el-table-column prop="content" label="内容"></el-table-column>
      <el-table-column prop="readNum" label="阅读数"></el-table-column>
      <el-table-column prop="like" label="点赞数"></el-table-column>
      <el-table-column prop="createdAt" label="创建时间"></el-table-column>
      <el-table-column prop="updatedAt" label="更新时间"></el-table-column>
    </el-table>
    <Row>
      <Col span="12" class="page">
        <Page :total="count" show-elevator @on-change="pageChange"/>
      </Col>
    </Row>
  </Card>
</template>
<script>
import axios from "axios";

export default {
  methods: {
    handleClick(row) {
      console.log(row);
    }
  },

  data() {
    return {
      tableData: [],
      count: 0,
      page: 1
    };
  },
  methods: {
    async getlist() {
      let data = await axios({
        method: "post",
        url: "/getPost",
        data: {
          page: this.page,
          where: {
            userId: 1
          }
        }
      });
      if (data.data.code == 1) {
        this.tableData = data.data.result.data.rows;
        this.count = data.data.result.data.count;
      }
    },
    pageChange(page) {
      this.page = page;
      this.getlist();
    },
    editPostTap(){
      this.$router.push({
        path:'/article/editPost'
      })
    }
  },
  created() {
    this.getlist();
  }
};
</script>
<style scoped>
.page {
  margin-top: 10px;
}
</style>
