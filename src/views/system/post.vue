<template>
  <div class="container">
    <!-- 条件查询 -->
    <el-form
      :model="postForm"
      :inline="true"
      style="width:97%;background-color:white"
    >
      <el-form-item>
        <el-input v-model="postForm.positionName" placeholder="岗位名称" />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" icon="el-icon-search" @click="searchPost()">
          查询
        </el-button>
        <el-button
          type="primary"
          icon="el-icon-circle-plus-outline"
          @click="addPost()"
        >
          新增
        </el-button>
      </el-form-item>
    </el-form>
    <!-- 表格数据 -->
    <el-table :data="postData" height="500px" border style="width:100%;">
      <el-table-column prop="positionId" label="岗位编号" width="200" />
      <el-table-column prop="positionCode" label="岗位编码" width="220" />
      <el-table-column prop="positionName" label="岗位名称" width="220" />
      <el-table-column prop="sequence" label="显示顺序" width="170" />
      <el-table-column prop="createTime" label="创建时间" width="280" />
      <el-table-column label="操作" width="200">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="editPost(scope.row)">
            编辑
          </el-button>
          <el-button type="text" size="small" @click="dletePost(scope.row)">
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>
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
    <!-- 新增 -->
    <common-add-post
      :addPostVisible="addPostVisible"
      @postRowClose="addPostClose"
    />
    <!-- 编辑 -->
    <common-edit-post
      :editPostVisible="editPostVisible"
      :editData="editPostData"
      @postRowClose="editPostClose"
    />
  </div>
</template>
<script>
import CommonAddPost from "../../components/Post/CommonAddPost";
import CommonEditPost from "../../components/Post/CommonEditPost";
export default {
  components: {
    CommonAddPost,
    CommonEditPost
  },
  data() {
    return {
      //搜索框
      postForm: {
        positionName: ""
      },
      // 表格数据
      postData: [],
      // 分页数据
      currentPage: 1,
      pageSize: 10,
      total: 0,
      // 编辑
      editPostVisible: false,
      editPostData: {},
      // 新增
      addPostVisible: false
    };
  },
  created() {
    this.postInit();
  },
  methods: {
    // 根据输入信息查询
    searchPost() {
      this.postRequest(
        "/position/findListsByPage?page=" +
          this.currentPage +
          "&size=" +
          this.pageSize,
        this.postForm
      ).then(resp => {
        if (resp) {
          this.postData = resp.data.records;
          this.total = resp.data.total;
          this.currentPage = resp.data.current;
          this.pageSize = resp.data.size;
        }
      });
    },
    // 根据id删除
    dletePost(val) {
      this.$confirm("确定删除该条数据", "警告", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          this.getRequest("/position/deleteById?ids=" + val.positionId).then(
            resp => {
              if (resp) {
                this.$message({
                  type: "success",
                  message: "删除成功!"
                });
              }
              this.postInit();
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
    //表格数据初始化
    postInit() {
      this.getRequest(
        "/position/findListByPage?page=" +
          this.currentPage +
          "&size=" +
          this.pageSize
      ).then(resp => {
        if (resp) {
          this.postData = resp.data.records;
          this.total = resp.data.total;
          this.currentPage = resp.data.current;
          this.pageSize = resp.data.size;
        }
      });
    },
    // 分页，页码大小改变
    handleSizeChange(val) {
      this.pageSize = val;
      this.postInit();
    },
    // 分页，当前页改变
    handleCurrentChange(val) {
      this.currentPage = val;
      this.postInit();
    },
    // 编辑
    editPost(val) {
      this.editPostData = val;
      this.editPostVisible = true;
    },
    // 关闭编辑框
    editPostClose() {
      this.editPostVisible = false;
    },
    // 新增
    addPost() {
      this.addPostVisible = true;
      this.postInit();
    },
    // 关闭新增框
    addPostClose() {
      this.addPostVisible = false;
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
