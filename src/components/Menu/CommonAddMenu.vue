<template>
  <el-dialog
    title="新增菜单"
    :visible.sync="addMenuVisible"
    width="60%"
    :before-close="addMenuClose"
  >
    <div class="dialogDiv">
      <el-form :model="addData" label-width="80px">
        <el-form-item label="上级菜单">
          <el-input v-model="addData.moduleName" />
        </el-form-item>
        <el-form-item label="id">
          <el-input v-model="addData.moduleId" />
        </el-form-item>
      </el-form>
      <el-form :model="addMenuData" label-width="80px">
        <el-form-item label="菜单名称">
          <el-input v-model="addMenuData.moduleName" />
        </el-form-item>
        <el-form-item label="父级id">
          <el-input v-model="addMenuData.parentModuleId" />
        </el-form-item>
        <el-form-item label="访问地址">
          <el-input v-model="addMenuData.moduleUrl" />
        </el-form-item>
        <el-form-item label="权限标识">
          <el-input v-model="addMenuData.permissionMark" />
        </el-form-item>
        <el-form-item label="类型">
          <el-select v-model="addMenuData.moduleType">
            <el-option label="目录" value="0" />
            <el-option label="菜单" value="1" />
            <el-option label="按钮" value="2" />
          </el-select>
        </el-form-item>
      </el-form>
    </div>
    <span slot="footer">
      <el-button type="primary" @click="saveAddMenu(), addMenuClose()">
        提交
      </el-button>
    </span>
  </el-dialog>
</template>
<script>
export default {
  props: {
    addMenuVisible: {
      type: Boolean
    },
    addData: {
      type: Object
    }
  },
  inject: ["reload"],
  data() {
    return {
      addMenuData: {
        moduleName: "",
        moduleUrl: "",
        moduleType: "",
        permissionMark: "",
        parentModuleId: ""
      }
    };
  },
  methods: {
    // 对话框父子组件传值
    addMenuClose() {
      this.$emit("menuRowClose");
    },
    // 菜单树
    saveAddMenu() {
      this.postRequest("/system/sysModule/insert", this.addMenuData).then(
        resp => {
          if (resp) {
            this.$message({
              message: "菜单新增成功!",
              type: "success"
            });
            this.reload();
          } else {
            this.$message.error("菜单新增失败，请重新提交!");
          }
        }
      );
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
