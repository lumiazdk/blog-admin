<template>
  <Card>
    <p slot="title">Banner</p>
    <a href="#" slot="extra" @click.prevent="addBannerTap">
      <Icon type="ios-loop-strong"></Icon>添加
    </a>
    <el-table :data="tableData" style="width: 100%" max-height="250">
      <el-table-column prop="name" label="标题"></el-table-column>
      <el-table-column label="背景">
        <template slot-scope="scope">
          <el-popover placement="right" trigger="click">
            <img
              :src="item"
              alt
              v-for="(item,k) in JSON.parse(scope.row.bannerPath)"
              :key="k"
              class="background"
            >
            <Button type="dashed" slot="reference">背景</Button>
          </el-popover>
        </template>
      </el-table-column>
      <el-table-column prop="createdAt" label="创建时间">
        <template slot-scope="scope">
          <i class="el-icon-time"></i>
          <span style="margin-left: 10px">{{ moment(scope.row.createdAt).format('YYYY.MM.DD')}}</span>
        </template>
      </el-table-column>
      <el-table-column prop="updatedAt" label="更新时间">
        <template slot-scope="scope">
          <i class="el-icon-time"></i>
          <span style="margin-left: 10px">{{ moment(scope.row.updatedAt).format('YYYY.MM.DD')}}</span>
        </template>
      </el-table-column>
      <el-table-column fixed="right" label="操作" width="150">
        <template slot-scope="scope">
          <el-button @click="editBannerTap(scope.row)" type="text" size="small">编辑</el-button>
          <el-button @click="delBannerTap(scope.row)" type="text" size="small">删除</el-button>
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
        url: "/getBanner",
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
    addBannerTap() {
      this.$router.push({
        path: "/article/addBanner"
      });
    },
    editBannerTap(item) {
      this.$router.push({
        path: "/article/editBanner",
        query: {
          id: item.id
        }
      });
    },
    delBannerTap(item) {
      this.$Modal.confirm({
        title: "确认删除此Banner?",
        onOk: () => {
          axios({
            method: "post",
            url: "/delBanner",
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
