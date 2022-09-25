<template>
  <div class="fillcontain">
    <head-top></head-top>
    <div class="table_container">
      <el-table
        v-loading="loading"
        @row-click="handelRowClick"
        :data="tableData"
        highlight-current-row
        style="width: 100%"
      >
        <el-table-column type="index" width="50"> </el-table-column>
        <el-table-column property="ArticleType" label="类型" width="120">
        </el-table-column>
        <el-table-column property="ArticleTitle" label="标题">
        </el-table-column>
        <el-table-column property="AuthorName" label="作者" width="420">
        </el-table-column>
        <el-table-column
          property="ArticlePublishDateTime"
          label="发布时间"
          width="180"
        >
        </el-table-column>
      </el-table>
      <div class="Pagination" style="text-align: left; margin-top: 10px">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="pageindex"
          :page-size="pagesize"
          layout="total, prev, pager, next"
          :total="total"
        >
        </el-pagination>
      </div>
    </div>

    <el-dialog
      title="信息详情"
      :visible.sync="dialogVisible"
      width="50%"
      :before-close="handleClose"
    >
      <div class="content">
        {{ currentInfo.ArticleContent }}
      </div>
    </el-dialog>
  </div>
</template>

<script>
import headTop from "../components/headTop";
export default {
  data() {
    return {
      tableData: [],
      total: 0,
      pageindex: 1,
      pagesize: 20,
      loading: false,
      dialogVisible: false,
      currentInfo: {},
    };
  },
  components: {
    headTop,
  },
  created() {
    this.querDataList();
  },
  methods: {
    handleClose() {
      this.dialogVisible = false;
      this.currentInfo = {};
    },
    handelRowClick(row) {
      this.dialogVisible = true;
      if (row.ArticleContent) {
        this.currentInfo = { ...row };
      } else {
        this.currentInfo = { ArticleContent: "抱歉，没有找到此行相对应的信息" };
      }
    },
    handleCurrentChange(pageindex) {
      this.pageindex = pageindex;
      this.querDataList();
    },
    handleSizeChange(pagesize) {
      this.pagesize = pagesize;
      this.querDataList();
    },
    querDataList() {
      this.loading = true;
      fetch(
        `https://api.gugudata.com/news/wxarticle/demo?type=HOT&pageindex=${this.pageindex}&pagesize=${this.pagesize}`,
        { method: "GET" }
      ).then((res) => {
        res
          .json()
          .then((result) => {
            this.tableData = result.Data;
            this.total = result.DataStatus.DataTotalCount;
          })
          .finally(() => {
            this.loading = false;
          });
      });
    },
  },
};
</script>

<style lang="less">
@import "../style/mixin";

.table_container {
  padding: 20px;
}
</style>
