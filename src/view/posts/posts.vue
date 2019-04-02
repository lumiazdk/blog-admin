<template>
  <Card>
    <p slot="title">文章</p>
    <a href="#" slot="extra" @click.prevent="addPostTap">
      <Icon type="ios-loop-strong"></Icon>添加
    </a>
    <el-table :data="tableData" style="width: 100%" max-height="250">
      <el-table-column prop="id" label="ID"></el-table-column>
      <el-table-column prop="title" label="标题"></el-table-column>
      <el-table-column label="背景">
        <template slot-scope="scope">
          <el-popover placement="right" trigger="click">
            <img
              :src="item"
              alt
              v-for="(item,k) in JSON.parse(scope.row.background)"
              :key="k"
              class="background"
            >
            <el-button slot="reference">查看</el-button>
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column prop="readNum" label="阅读数"></el-table-column>
      <el-table-column prop="like" label="点赞数"></el-table-column>
      <el-table-column prop="createdAt" label="创建时间">
        <template slot-scope="scope">
          <i class="el-icon-time"></i>
          <span
            style="margin-left: 10px"
          >{{ moment(scope.row.createdAt).format('MMMM Do YYYY, h:mm:ss a')}}</span>
        </template>
      </el-table-column>
      <el-table-column prop="updatedAt" label="更新时间">
        <template slot-scope="scope">
          <i class="el-icon-time"></i>
          <span
            style="margin-left: 10px"
          >{{ moment(scope.row.updatedAt).format('MMMM Do YYYY, h:mm:ss a')}}</span>
        </template>
      </el-table-column>
      <el-table-column fixed="right" label="操作" width="150">
        <template slot-scope="scope">
          <el-button @click="editPostTap(scope.row)" type="text" size="small">编辑</el-button>
          <el-button @click="delPostTap(scope.row)" type="text" size="small">删除</el-button>
          <el-button @click="postDetailTap(scope.row)" type="text" size="small">详情</el-button>
        </template>
      </el-table-column>
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
import moment from "moment";
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
          where: {}
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
    addPostTap() {
      this.$router.push({
        path: "/article/addPost"
      });
    },
    editPostTap(item) {
      this.$router.push({
        path: "/article/editPost",
        query: {
          id: item.id
        }
      });
    },
    delPostTap(item) {
      this.$Modal.confirm({
        title: "确认删除本条文章？",
        onOk: () => {
          axios({
            method: "post",
            url: "/delPost",
            data: {
              id: item.id
            }
          }).then(res => {
            if (res.data.code == 1) {
              this.$Message.info("删除成功");
              this.getlist();
            } else {
              this.$Message.error("删除失败");
            }
          });
        },
        onCancel: () => {
          this.$Message.info("已取消");
        }
      });
    },
    postDetailTap(item) {
      this.$router.push({
        path: "/article/postDetail",
        query: {
          id: item.id
        }
      });
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
.background {
  height: 100px;
  width: 200px;
  padding: 5px;
}
</style>
