<template>
  <el-container class="layout-content">
    <!-- 左侧菜单 -->
    <el-aside width="200px">
      <slot name="left"></slot>
    </el-aside>

    <!-- 右侧内容 -->
    <el-main>
      <!-- 面包屑 -->
      <div class="top">
        <i class="fa fa-reordr"></i>
        <el-breadcrumb class="breadcrumb" separator="/">
          <el-breadcrumb-item v-for="(item,index) in breadCrumbItems" :key="index" :to="item.path">{{item.title}}</el-breadcrumb-item>
        </el-breadcrumb>
      </div>
      <!-- 页面内容 -->
      <div class="content">
        <slot name="content"></slot>
      </div>
    </el-main>
  </el-container>
</template>
<script lang="ts">
import { Component, Vue,Provide,Watch} from 'vue-property-decorator'
@Component({
  components:{}
})

export default class Content extends Vue{
  @Provide() breadCrumbItems:any; //面包屑数组

  created(){
    this.initBreadCrumbItems(this.$route)
  }

  @Watch("$route") handleRouteChange(to:any){
    this.initBreadCrumbItems(to)
  }

  initBreadCrumbItems(router:any) {
    // console.log(router)
    let breadCrumbItems:any = [{path:"/",title:"后台管理系统"}];
    // 遍历父级当期子路由的页面的title和path，存到数组
    for (const index in router.matched) {
      if(router.matched[index].meta && router.matched[index].meta.title){
        breadCrumbItems.push({
          path:router.matched[index].path ? router.matched[index].path:"/",
          title:router.matched[index].meta.title
        })
      }
    }
    this.breadCrumbItems=breadCrumbItems;
  }
}

</script>
<style lang="scss" scoped>
.layout-content {
  width: 100%;
  height: 100%;
  .el-main {
    padding: 0;
    .top {
      background: #fff;
      height: 54px;
      border-right: none;
      border-bottom: 1px solid #e6e6e6;
      display: flex;
      align-items: center;
      i {
        font-size: 20px;
        cursor: pointer;
        padding-left: 16px;
      }
      .breadcrumb {
        padding-left: 16px;
      }
    }
    .content {
      padding: 10px;
      height: calc(100% - 54px);
      box-sizing: border-box;
    }
  }
}
</style>