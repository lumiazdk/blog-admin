<template>
  <Card class="card">
    <p slot="title">添加Banner</p>
    <a href="#" slot="extra" @click="handleSubmit('formValidate')">
      <Icon type="ios-loop-strong"></Icon>添加
    </a>
    <Form
      ref="formValidate"
      :model="formValidate"
      :rules="ruleValidate"
      label-position="right"
      :label-width="100"
    >
      <Row>
        <Col span="11" class="col">
          <FormItem label="标题" prop="name">
            <Input v-model="formValidate.name" placeholder="输入您的标题"></Input>
          </FormItem>
        </Col>
        <Col span="11" class="col">
          <FormItem label="背景" prop="bannerPath">
            <div class="demo-upload-list" v-for="item in uploadList" :key="item">
              <template>
                <img :src="item">
                <div class="demo-upload-list-cover">
                  <Icon type="ios-eye-outline" @click.native="handleView(item)"></Icon>
                  <Icon type="ios-trash-outline" @click.native="handleRemove(item)"></Icon>
                </div>
              </template>
            </div>
            <div class="ivu-upload" style="display: inline-block; width: 58px;">
              <div class="ivu-upload ivu-upload-drag">
                <input
                  type="file"
                  multiple="multiple"
                  class="ivu-upload-input"
                  id="input"
                  @change="uploadImg"
                >
                <label for="input">
                  <div style="width: 58px; height: 58px; line-height: 58px;">
                    <i class="ivu-icon ivu-icon-ios-camera" style="font-size: 20px;"></i>
                  </div>
                </label>
              </div>
            </div>
            <Modal title="预览" v-model="visible">
              <img :src="imgName" v-if="visible" style="width: 100%">
            </Modal>
          </FormItem>
        </Col>
      </Row>
    </Form>
  </Card>
</template>
<script>
import axios from "axios";
export default {
  data() {
    return {
      formValidate: {
        name: "",
        bannerPath: ""
      },
      ruleValidate: {
        name: [
          {
            required: true,
            message: "标题不能为空",
            trigger: "blur"
          }
        ],

        bannerPath: [
          {
            required: true,
            message: "Banner不能为空",
            trigger: "blur"
          }
        ]
      },
      file: null,
      defaultList: [],
      imgName: "",
      visible: false,
      uploadList: []
    };
  },
  methods: {
    handleView(item) {
      this.imgName = item;
      this.visible = true;
    },
    handleRemove(file) {
      this.uploadList.splice(this.uploadList.indexOf(file), 1);
    },
    uploadImg() {
      let file = document.getElementById("input").files[0];
      var formData = new FormData();
      formData.append("file", file);
      axios({
        method: "post",
        url: "/upload",
        data: formData
      }).then(res => {
        this.uploadList.push(res.data.url);
      });
    },

    submit() {
      if (this.uploadList.length == 0) {
        this.$Message.error("请选择背景");
        return;
      }
      axios({
        method: "post",
        url: "/addBanner",
        data: {
          name: this.formValidate.name,
          bannerPath: JSON.stringify(this.uploadList)
        }
      }).then(res => {
        if (res.data.code == 1) {
          this.$Message.info("添加成功");
        } else {
          this.$Message.error("添加失败");
        }
      });
    },
    handleSubmit(name) {
      let that = this;
      this.$refs[name].validate(valid => {
        if (valid) {
          that.submit();
        } else {
          this.$Message.error("表单未完成!");
        }
      });
    }
  },
  mounted() {},
  created() {},
  watch: {
    uploadList() {
      console.log(2);
      if (this.uploadList.length == 0) {
        this.formValidate.bannerPath = "";
      } else {
        this.formValidate.bannerPath = "value";
      }
    }
  }
};
</script>
<style scoped>
.col {
  padding: 10px;
}
.demo-upload-list {
  display: inline-block;
  width: 60px;
  height: 60px;
  text-align: center;
  line-height: 60px;
  border: 1px solid transparent;
  border-radius: 4px;
  overflow: hidden;
  background: #fff;
  position: relative;
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.2);
  margin-right: 4px;
}
.demo-upload-list img {
  width: 100%;
  height: 100%;
}
.demo-upload-list-cover {
  display: none;
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background: rgba(0, 0, 0, 0.6);
}
.demo-upload-list:hover .demo-upload-list-cover {
  display: block;
}
.demo-upload-list-cover i {
  color: #fff;
  font-size: 20px;
  cursor: pointer;
  margin: 0 2px;
}
.card {
  min-height: 500px;
}
</style>
