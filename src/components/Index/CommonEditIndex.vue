<template>
  <el-dialog
    title="考核指标编辑"
    :visible.sync="editIndexVisible"
    width="60%"
    :before-close="editIndexClose"
  >
    <div class="dialogDiv">
      <el-form :model="editData" label-width="80px">
        <el-form-item label="指标名称">
          <el-input v-model="editData.indexName" />
        </el-form-item>
        <el-form-item label="分值">
          <el-input v-model="editData.scoreWeight" />
        </el-form-item>
        <el-form-item label="排列顺序">
          <el-input v-model="editData.sequence" />
        </el-form-item>
        <el-form-item label="考核模板">
          <el-select v-model="editData.template">
            <el-option
              v-for="(item, index) in this.template"
              :key="index"
              :label="item.templateName"
              :value="item.examineTId"
            />
          </el-select>
        </el-form-item>
        <el-button
          type="text"
          class="el-icon-circle-plus-outline"
          @click="addIndexDetail()"
        >
          添加考核指标明细
        </el-button>
        <div style="margin-left:0px">
          <el-form-item
            v-for="(item, index) in editData.sysTCodeInforList"
            :key="index"
          >
            <el-row>
              <el-col :span="6">
                <el-form-item label="考核内容">
                  <el-input v-model="item.examineContent" />
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label="工作要求">
                  <el-input v-model="item.requirement" />
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label="考核标准">
                  <el-input v-model="item.examineStandard" />
                </el-form-item>
              </el-col>
              <el-col :span="6">
                <el-form-item label="分值">
                  <el-input v-model="item.score" />
                </el-form-item>
              </el-col>
              <el-button
                type="text"
                style="margin-left:20px"
                @click="dlete(item)"
              >
                删除
              </el-button>
            </el-row>
          </el-form-item>
        </div>
      </el-form>
    </div>
    <span slot="footer">
      <el-button type="primary" @click="saveEditIndex(), editIndexClose()">
        提交
      </el-button>
    </span>
  </el-dialog>
</template>
<script>
export default {
  props: {
    editIndexVisible: {
      type: Boolean
    },
    editData: {
      type: Object
    }
  },
  data() {
    return {
      template: []
    };
  },
  created() {
    this.templateInit();
  },
  methods: {
    // 对话框父子组件传值
    editIndexClose() {
      this.$emit("editClose");
    },
    // 添加
    addIndexDetail() {
      this.editData.sysTCodeInforList.push({
        codeName: " ",
        codeValue: " ",
        valueType: " ",
        description: " "
      });
    },
    // 删除
    dlete(val) {
      var index = this.editData.sysTCodeInforList.indexOf(val);
      if (index !== -1) {
        this.addData.sysTCodeInforList.splice(index, 1);
      }
    },
    //表格数据初始化
    templateInit() {
      this.getRequest("/examine/templateInfor/queryAll").then(resp => {
        if (resp) {
          this.template = resp.data;
        }
      });
    },
    // 保存修改后的信息
    saveEditIndex() {
      this.postRequest("/", this.editData).then(resp => {
        if (resp) {
          this.$message({
            message: "信息更改成功!",
            type: "success"
          });
        } else {
          this.$message.error("信息更改失败，请重新提交!");
        }
      });
    }
  }
};
</script>

<style lang="less" scoped>
.dialogDiv {
  height: 400px;
  overflow: auto;
}
</style>
<style lang="less" scoped>
.el-input {
  width: 700px;
}
</style>
