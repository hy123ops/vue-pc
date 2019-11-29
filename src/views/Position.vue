<template>
  <div>
    <Table border :columns="columns" :data="data">
      <div slot="header">
        <Button @click="addPosition" type="success" style="margin-left: 5px">添加电影</Button>
      </div>
      <template slot-scope="{ row }">
        <strong>{{ row.name }}</strong>
      </template>
      <template slot-scope="{ row, index }" slot="action">
        <Button type="primary" size="small" style="margin-right: 5px" @click="show(index)">View</Button>
        <Button type="error" size="small" @click="remove(index)">Delete</Button>
      </template>
    </Table>
    <div slot="footer">
      <Pagination @onPageInfo="handlePageInfo"></Pagination>
    </div>
    <Modal v-model="showModal" title="添加电影" :width="800" @on-ok="handleOkClick">
      <Form :model="formData" ref="formValidate" :label-width="80">
        <FormItem label="海报">
          <Upload
            name="fileLogo"
            :before-upload="handleUpload"
            action="/api/position/upload"
            :on-success="handleSucc"
          >
            <Button icon="ios-cloud-upload-outline">点击上传图片</Button>
          </Upload>
          <div v-if="file !== null">
            <img :src="insideSrc" alt style="width: 60px; height: 60px;" />
            <!-- <Button
              type="text"
              @click="upload"
              :loading="loadingStatus"
            >{{ loadingStatus ? 'Uploading' : 'Click to upload' }}</Button>-->
          </div>
        </FormItem>
        <FormItem label="片名">
          <Input v-model="formData.fileName" placeholder="请输入片名" />
        </FormItem>
        <FormItem label="版本">
          <Input v-model="formData.version" placeholder="请输入版本" />
        </FormItem>
        <FormItem label="主演">
          <Input v-model="formData.starName" placeholder="请输入主演" />
        </FormItem>
        <FormItem label="评分">
          <Input v-model="formData.sc" placeholder="请输入评分" />
        </FormItem>
        <FormItem label="想看">
          <Input v-model="formData.wish" placeholder="请输入想看" />
        </FormItem>
        <FormItem label="场次">
          <Input v-model="formData.showInfo" placeholder="请输入场次" />
        </FormItem>
        <FormItem label="上映时间">
          <Input v-model="formData.showData" placeholder="请输入上映时间" />
        </FormItem>
        <FormItem label="简介">
          <Input v-model="formData.dra" placeholder="请输入简介" />
        </FormItem>
        <FormItem label="发布时间">
          <Row>
            <Col span="6">
              <FormItem prop="date">
                <DatePicker
                  :value="date"
                  @on-change="handleDateChange"
                  type="date"
                  placeholder="Select date"
                ></DatePicker>
              </FormItem>
            </Col>
            <Col span="2" style="text-align: center">-</Col>
            <Col span="11">
              <FormItem prop="time">
                <TimePicker
                  :value="time"
                  @on-change="handleTimeChange"
                  type="time"
                  placeholder="Select time"
                ></TimePicker>
              </FormItem>
            </Col>
          </Row>
        </FormItem>
      </Form>
    </Modal>
  </div>
</template>
<script>

import Pagination from "./Pagination";
import { get,post } from "../utils/http";
import _ from "lodash";
import moment from "moment";
export default {
  data() {
    return {
      formData: {
        fileName: "",
        version: "",
        starName: "",
        sc: "",
        wish: "",
        showInfo: "",
        showData: "",
        dra: "",
        createTime: "",
        fileLogo:''
      },
      columns: [
        {
          title: "海报",
          key: "fileLogo",
          className: "img",
          render: (h, { row, column, index }) => {
            return h(
              "img",
              {
                attrs: {
                  src: "http://localhost:3000/uploads/" + row.fileLogo
                }
              },
              null
            );
          }
        },
        {
          title: "片名",
          key: "fileName"
        },
        {
          title: "版本",
          key: "version"
        },
        {
          title: "主演",
          key: "starName"
        },
        {
          title: "评分",
          key: "sc"
        },
        {
          title: "想看",
          key: "wish"
        },
        {
          title: "场次",
          key: "showInfo"
        },
        {
          title: "上映时间",
          key: "showData"
        },
        {
          title: "简介",
          key: "dra"
        },
        {
          title: "发布时间",
          key: "createTime"
        },
        {
          title: "操作",
          slot: "action",
          width: 150,
          align: "center"
        }
      ],
      data: [],
      resource: [],
      pageNo: 1,
      pageSize: 5,
      showModal: false,
      file: null,
      loadingStatus: false,
      insideSrc: "",
      date: new Date(),
      time: new Date()
    };
  },

  async mounted() {
    let result = await get("/api/position?start=0&count=11");
    this.resource = result.list;
    this.data = _.chunk(this.resource, this.pageSize)[this.pageNo - 1];
  },
  beforeRouteEnter(to, from, next) {
    next(vm => {
      vm.$emit("onRouteChange", to);
    });
  },
  methods: {
    show(index) {
      this.$Modal.info({
        title: "User Info",
        content: `Name：${this.data6[index].name}<br>Age：${this.data6[index].age}<br>Address：${this.data6[index].address}`
      });
    },
    remove(index) {
      this.data6.splice(index, 1);
    },
    addPosition() {
      this.showModal = true;
    },
    handlePageInfo({ pageNo, pageSize }) {
      this.pageNo = pageNo;
      this.pageSize = pageSize;
    },
    handleUpload(file) {
      this.file = file;

      // 转换为base64
      const reader = new FileReader();
      // 将读取到的文件编码成Data URL
      reader.readAsDataURL(file);
      reader.onload = event => {
        this.insideSrc = event.srcElement.result;
      };
    },
    upload() {
      this.loadingStatus = true;
      setTimeout(() => {
        this.file = null;
        this.loadingStatus = false;
        this.$Message.success("Success");
      }, 1500);
    },

    handleSucc(response, file, fileList) {
      let ret = response.ret;
      if (ret) {
        let filename = response.data.filename;
        this.formData.fileLogo = filename;
        this.file = null;
      } else {
        this.$Message.error("图片上传失败.");
      }
    },

    handleDateChange(date) {
      this.formData.createTime = date + this.time; 
    },
    handleTimeChange(time) {
      this.formData.createTime = this.date + time;
    },
    async handleOkClick() {
      if (this.formData.createTime === "") {
        this.formData.createTime = moment().format("YYYY-MM-DD, HH:mm:ss");
      }
      if (this.formData.fileLogo) {
        let result = await post("/api/position", this.formData);
        this.$router.go(0);
      } else {
        this.$Message.error("error.");
      }
    }
  },

  watch: {
    pageNo() {
      this.data = _.chunk(this.resource, this.pageSize)[this.pageNo - 1];
    },
    pageSize() {
      this.data = _.chunk(this.resource, this.pageSize)[0];
    }
  },
  components: {
    Pagination
  }
};
</script>

<style lang="less">
.ivu-table-footer {
  height: 60px !important;
  padding-right: 20px;
}
.img {
  img {
    width: 60px;
    height: 60px;
  }
}
</style>
