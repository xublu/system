<template>
  <el-dialog
    title="菜单编辑"
    :visible.sync="editMenuVisible"
    width="60%"
    :before-close="editMenuClose"
  >
    <div class="dialogDiv">
      <el-form :model="editData" label-width="80px">
        <el-form-item label="菜单名称">
          <el-input v-model="editData.moduleName" />
        </el-form-item>
        <el-form-item label="访问地址">
          <el-input v-model="editData.moduleUrl" />
        </el-form-item>
        <el-form-item label="权限标识">
          <el-input v-model="editData.permissionMark" />
        </el-form-item>
        <el-form-item label="类型">
          <el-select v-model="editData.moduleType">
            <el-option label="目录" value="0" />
            <el-option label="菜单" value="1" />
            <el-option label="按钮" value="2" />
          </el-select>
        </el-form-item>
      </el-form>
    </div>
    <span slot="footer">
      <el-button type="primary" @click="saveEditMenu(), editMenuClose()">
        提交
      </el-button>
    </span>
  </el-dialog>
</template>
<script>
export default {
  props: {
    editMenuVisible: {
      type: Boolean
    },
    editData: {
      type: Object
    }
  },
  data() {
    return {};
  },
  methods: {
    // 对话框父子组件传值
    editMenuClose() {
      this.$emit("menuRowClose");
    },
    // 保存修改后的信息
    saveEditMenu() {
      this.postRequest("/system/sysModule/update", this.editData).then(resp => {
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
