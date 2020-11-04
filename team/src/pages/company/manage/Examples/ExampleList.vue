<template>
  <div class="manageItemList">
    <div class="headerSearchDiv">
      <el-form :inline="true" class="demo-form-inline headerSearchForm" label-width="68px">
        <el-form-item label="">
          <span class="headerTitle">数据实例列表：</span>
        </el-form-item>
      </el-form>
      <el-button type="primary" icon="el-icon-plus" class="noOutLine headerButton uploadButton pc" @click="toUploadExample">新增实例</el-button>
      <el-button type="primary" icon="el-icon-plus" class="noOutLine headerButton uploadButton mobile" @click="toUploadExample">新增</el-button>
    </div>
    <div class="manageContentDiv">
      <el-table
        v-loading="loading"
        element-loading-text="数据加载中"
        element-loading-spinner="el-icon-loading"
        :data="quList.queryListVOS"
        class="my-table table-list">
        <el-table-column
          prop="quName"
          label="实例名称"
          min-width="200">
        </el-table-column>
        <el-table-column
          prop="quTime"
          label="上传时间"
          min-width="160">
        </el-table-column>
        <el-table-column
          label="操作"
          width="160">
          <template slot-scope="scope">
            <el-button type="success" class="noOutLine" size="mini" @click="seeDetail(scope.row.quId)">查看详情</el-button>
          </template>
        </el-table-column>
      </el-table>
      <div class="pages" v-if="quList.total>0">
        <el-pagination
          layout="prev, pager, next"
          :total="quList.total*1"
          :current-page="pageNum*1"
          :page-size="pageSize*1"
          @current-change="(currentPage)=>reqQus(currentPage)"
        >
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
  import {REQAllINTER} from '../../../../api/types'
  import {mapState,mapActions} from 'vuex'
  export default {
    name: 'manageModelList',
    data () {
      return {
        pageNum: 1,
        pageSize: 13,
        showFlag:1,   //是否可见标志，1为显示，0为不显示

        qu:{},        //单一模型的详细信息
        quList:[],    //模型数据列表

        loading:false,  //加载中
      }
    },
    methods: {
      ...mapActions(['choiceMenuId']),

      //获取数据实例列表,传入num的话就请求num页数据，否则请求第一页数据
      async reqQus(num){
        this.pageNum = num || 1
        var {pageNum,pageSize,showFlag} = this
        this.loading = true
        var result = await REQAllINTER({pageNum,pageSize,showFlag})
        this.loading = false
        if(result && result.status === 0){
          this.quList = result.data
          this.showFlag = showFlag
        }else{
          if(result.msg){
            var message = result.msg
          }else{
            var message = '获取数据实例失败'
          }
          this.message(message,'error',1500)
        }
      },

      //查看详情
      seeDetail(quId){
        var {showFlag} = this
        this.$router.push({name:'ExampleDetail',query:{quId,showFlag}})
      },
      //前往上传实例
      toUploadExample(){
        this.$router.push({name:'UploadExample'})
      },

      //消息提示
      message(message,type,duration){
        this.$message({
          message,type,duration
        })
      }
    },
    mounted () {
      this.choiceMenuId('ExampleList')
      this.reqQus()
    },
  }
</script>

<style lang="less">
  @import "../../../../assets/css/table/table";
  @import "../../../../assets/css/input/input";
  @import "../../../../assets/css/Item-List/item-list-pc";
  @media screen and (max-width: 767px) {
    @import "../../../../assets/css/Item-List/item-list-mobile";
  }
</style>
