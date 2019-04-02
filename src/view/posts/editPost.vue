<template>
  <Card>
    <p slot="title">编辑文章</p>
    <Form
      ref="formValidate"
      :model="formValidate"
      :rules="ruleValidate"
      label-position="right"
      :label-width="100"
    >
      <Row>
        <Col span="11" class="col">
          <FormItem label="Name" prop="name">
            <Input v-model="formValidate.name" placeholder="Enter your name"></Input>
          </FormItem>
        </Col>
        <Col span="11" class="col">
          <FormItem label="Name" prop="name">
            <Input v-model="formValidate.name" placeholder="Enter your name"></Input>
          </FormItem>
        </Col>
      </Row>
      <Row>
        <Col span="11" class="col">
          <FormItem label="Name" prop="name">
            <Upload :before-upload="handleUpload" action="//jsonplaceholder.typicode.com/posts/">
              <Button icon="ios-cloud-upload-outline">Select the file to upload</Button>
            </Upload>
          </FormItem>
        </Col>
        <Col span="11" class="col">
          <FormItem label="Name" prop="name">
            <Input v-model="formValidate.name" placeholder="Enter your name"></Input>
          </FormItem>
        </Col>
      </Row>
      <FormItem>
        <Button type="primary" @click="handleSubmit('formValidate')">提交</Button>
      </FormItem>
    </Form>
  </Card>
</template>
<script>
export default {
  data() {
    return {
      formValidate: {
        name: ""
      },
      ruleValidate: {
        name: [
          {
            required: true,
            message: "The name cannot be empty",
            trigger: "blur"
          }
        ]
      },
      file: null,
      loadingStatus: false
    };
  },
  methods: {
    handleUpload(file) {
      this.file = file;
      setTimeout(() => {
        this.file = null;
        this.$Message.success("Success");
      }, 1500);
      return false;
    }
  }
};
</script>
<style scoped>
.col {
  padding: 10px;
}
</style>
