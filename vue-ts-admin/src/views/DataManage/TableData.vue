<template>
  <div class="table-data">
    <div class="search-box">
      <el-input size="small"
                v-model="searchVal"
                placeholder="请输入课程名称检索"></el-input>
      <el-button size="small"
                 type="primary"
                 icon="el-icon-search"
                 @click="handleSearch">搜索</el-button>
    </div>
    <el-table :data="tableData"
              border
              :height="tHeight"
              class="table-box"
              style="width:100%">
      <el-table-column type="index"
                       label="序号"
                       width="60"> </el-table-column>
      <el-table-column label="课程名称"
                       prop="title"> </el-table-column>
      <el-table-column label="课程等级"
                       prop="level"
                       width="120">
      </el-table-column>
      <el-table-column label="技术栈"
                       prop="type"
                       width="120">
      </el-table-column>
      <el-table-column label="报名人数"
                       prop="count"
                       width="120">
      </el-table-column>
      <el-table-column label="上线日期"
                       prop="date"
                       width="160">
      </el-table-column>
      <el-table-column label="操作"
                       width="160">
        <template slot-scope="scope">
          <el-button size="mini"
                     @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
          <el-button size="mini"
                     type="danger"
                     @click="handleDelete(scope.$index, scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <div class="pages"
         ref="page-box">
      <el-pagination @size-change="handleSizeChange"
                     @current-change="handleCurrentChange"
                     :page-sizes="[5, 15, 20]"
                     :page-size="size"
                     layout="total,sizes,prev,pager,next,jumper"
                     :total="total"></el-pagination>
    </div>
    <EditDialog :dialogVisible="dialogVisible"
                :form="formData"
                @closeDialog="closeDialog"></EditDialog>
  </div>
</template>
<script lang="ts">
import EditDialog from "./EditDialog.vue";
import { Component, Vue, Provide } from "vue-property-decorator";
@Component({
  components: { EditDialog }
})
export default class TableData extends Vue {
  @Provide() searchVal: string = ""; //搜索
  @Provide() tHeight: number = document.body.offsetHeight - 270;
  @Provide() tableData: any = []; //数据
  @Provide() page: number = 1; //当前page
  @Provide() size: number = 5; //个数
  @Provide() total: number = 0; //总数
  @Provide() dialogVisible: Boolean = false; //是否展示编辑页面
  @Provide() formData: Object = {
    title: "",
    type: "",
    level: "",
    count: "",
    date: ""
  };
  loadData() {
    (this as any)
      .$axios(`/api/profiles/loadMore/${this.page}/${this.size}`)
      .then((res: any) => {
        console.log(res);
        this.tableData = res.data.data.list;
        this.total = res.data.data.total;
      });
  }
  handleSizeChange(val: number): void {
    this.size = val;
    this.page = 1;
    this.searchVal ? this.loadSearchData() : this.loadData();
  }
  handleCurrentChange(val: number): void {
    this.page = val;
    this.searchVal ? this.loadSearchData() : this.loadData();
  }
  handleSearch(): void {
    // 搜索
    this.page = 1;
    this.searchVal ? this.loadSearchData() : this.loadData();
  }
  loadSearchData() {
    // 加载搜索数据
    (this as any)
      .$axios(
        `/api/profiles/search/${this.searchVal}/${this.page}/${this.page}`
      )
      .then((res: any) => {
        this.tableData = res.data.datas.list;
        this.total = res.data.datas.total;
      });
  }
  handleEdit(index: number, row: any) {
    this.formData = row;
    this.dialogVisible = true;
  }
  closeDialog() {
    this.dialogVisible = false;
  }
  handleDelete(index, row) {
    console.log(index, row);
    (this as any).$axios
      .delete(`/api/profiles/delete/${row._id}`)
      .then((res: any) => {
        this.$message({
          message: res.data.msg,
          type: "success"
        });
        this.tableData.splice(index, 1);
      });
  }
  created() {
    this.loadData();
  }
}
</script>
<style lang="scss" scoped>
.table-data {
  height: 100%;
  .table-box {
    font-size: 14px;
  }
  .pages {
    background: #fff;
    margin-top: 10px;
    padding: 10px 10px;
    text-align: right;
    height: 55px;
    box-sizing: border-box;
  }
  .search-box {
    background: #fff;
    margin-bottom: 10px;
    padding: 10px 10px;
    border-radius: 4px;
    height: 55px;
    box-sizing: border-box;
    .el-input {
      width: 200px;
      margin-right: 10px;
    }
  }
}
</style>
