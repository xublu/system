<template>
  <div class="container">
    <!-- 条件查询 -->
    <el-form
      :model="dicFrom"
      :inline="true"
      style="width:97%;background-color:white"
    >
      <el-form-item>
        <el-input v-model="dicFrom.codeTName" placeholder="编码名称" />
      </el-form-item>
      <el-form-item>
        <el-input v-model="dicFrom.codeType" placeholder="编码类型" />
      </el-form-item>
      <el-form-item>
        <el-button
          type="primary"
          icon="el-icon-search"
          @click="searchDictionary()"
        >
          查询
        </el-button>
        <el-button type="primary" icon="el-icon-plus" @click="addDic()">
          新增
        </el-button>
        <el-button type="primary" icon="el-icon-delete" @click="selectdelete()">
          批量删除
        </el-button>
      </el-form-item>
    </el-form>
    <!-- 表格数据 -->
    <el-table
      :data="dicData"
      height="500px"
      border
      style="width:100%"
      @selection-change="handleSelectionChange"
    >
      <el-table-column type="selection" width="100" />
      <el-table-column prop="codeTName" label="编码名称" width="300" />
      <el-table-column prop="codeType" label="编码类型" width="300" />
      <el-table-column prop="createTime" label="创建时间" width="320" />
      <el-table-column label="操作" width="260">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="editDic(scope.row)">
            编辑
          </el-button>
          <el-button type="text" size="small" @click="sinDelete(scope.row)">
            删除
          </el-button>
          <el-button type="text" size="small" @click="detailsDic(scope.row)">
            查看详情
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 新增 -->
    <common-add-dic :addDicVisible="addDicVisible" @addClose="addDicClose" />
    <!-- 编辑 -->
    <common-edit-dic
      :editDicVisible="editDicVisible"
      @editClose="editDicClose"
      :editData="editData"
    />
    <!-- 查看详情 -->
    <common-details-dic
      :detailsDicVisible="detailsDicVisible"
      @detailsClose="detailsDicClose"
      :detailsData="detailsData"
    />
    <!-- 分页 -->
    <div style="width:98%;background-color:white">
      <el-pagination
        :current-page.sync="currentPage"
        :page-size="pageSize"
        :total="total"
        :page-sizes="[10, 20, 30, 40, 50]"
        layout="total, prev, pager, next, jumper, sizes"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
      />
    </div>
  </div>
</template>
<script>
import CommonAddDic from "../../components/Dic/CommonAddDic";
import CommonEditDic from "../../components/Dic/CommonEditDic";
import CommonDetailsDic from "../../components/Dic/CommonDetailsDic";
export default {
  components: {
    CommonAddDic,
    CommonEditDic,
    CommonDetailsDic
  },
  data() {
    return {
      //搜索框
      dicFrom: {
        codeTName: "",
        codeType: ""
      },
      // 表格数据
      dicData: [],
      // 选中要删除的数据
      selectData: [],
      // 新增
      addDicVisible: false,
      // 编辑
      editDicVisible: false,
      editData: {},
      // 查看详情
      detailsDicVisible: false,
      detailsData: {},
      // 分页数据
      currentPage: 1,
      pageSize: 10,
      total: 0
    };
  },
  created() {
    this.dicInit();
  },
  methods: {
    // 根据输入信息查询
    searchDictionary() {
      this.getRequest(
        "/system/codeType/codeTypes1?codeTName=" +
          this.dicFrom.codeTName +
          "&codeType=" +
          this.dicFrom.codeType +
          "&current=" +
          this.currentPage +
          "&pageSize=" +
          this.pageSize
      ).then(resp => {
        if (resp) {
          this.dicData = resp.data.records;
          this.total = resp.data.total;
          this.currentPage = resp.data.current;
          this.pageSize = resp.data.size;
        }
      });
    },
    //表格数据初始化
    dicInit() {
      this.getRequest(
        "/system/codeType/codeTypeByPage?current=" +
          this.currentPage +
          "&pageSize=" +
          this.pageSize
      ).then(resp => {
        if (resp) {
          this.dicData = resp.data.records;
          this.total = resp.data.total;
          this.currentPage = resp.data.current;
          this.pageSize = resp.data.size;
        }
      });
    },
    // 新增字典
    addDic() {
      this.addDicVisible = true;
    },
    // 关闭新增对话框
    addDicClose() {
      this.addDicVisible = false;
    },
    // 表格多选
    handleSelectionChange(val) {
      this.selectData = val;
    },
    // 批量删除
    selectdelete() {
      var checkArray = this.selectData;
      var idArray = [];
      checkArray.forEach(function(item) {
        idArray.push(item.codeTypeId);
      });
      this.$confirm("确定删除您勾选的数据", "警告", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          this.deleteRequest("/system/codeType/codeTypes", idArray).then(
            resp => {
              if (resp) {
                this.$message({
                  type: "success",
                  message: "删除成功!"
                });
              }
              this.dicInit();
            }
          );
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    },
    // 单个删除
    sinDelete(val) {
      this.$confirm("确定删除该条数据", "警告", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          this.deleteRequest(
            "/system/codeType/codeType/" + val.codeTypeId
          ).then(resp => {
            if (resp) {
              this.$message({
                type: "success",
                message: "删除成功!"
              });
            }
            this.dicInit();
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    },
    // 编辑字典
    editDic(val) {
      console.log(val);
      this.editData = val;
      this.editDicVisible = true;
    },
    // 关闭编辑对话框
    editDicClose() {
      this.editDicVisible = false;
    },
    // 查看字典详情
    detailsDic(val) {
      this.detailsData = val;
      this.detailsDicVisible = true;
    },
    // 关闭查看详情对话框
    detailsDicClose() {
      this.detailsDicVisible = false;
    },
    // 分页，页码大小改变
    handleSizeChange(val) {
      this.pageSize = val;
      this.dicInit();
    },
    // 分页，当前页改变
    handleCurrentChange(val) {
      this.currentPage = val;
      this.dicInit();
    }
  }
};
</script>
<style lang="less" scoped>
.container {
  width: 98%;
  margin: 5px;
  background-color: white;
}
</style>
