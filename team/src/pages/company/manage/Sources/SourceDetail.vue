<template>
  <div class="manageItemList manageItemDetail manageSourceDetail"
       v-loading="loading"
       element-loading-text="数据加载中"
       element-loading-spinner="el-icon-loading"
       element-loading-background="rgba(0, 0, 0, 0.1)">
    <el-dialog title="基本表字段详情" :visible.sync="dialogTableVisible" top="15vh" width="700px">
      <el-table
        :data="table"
        v-loading="loading1"
        style="min-height: 300px;"
        element-loading-text="数据加载中"
        element-loading-spinner="el-icon-loading"
        element-loading-background="rgba(0, 0, 0, 0.1)"
        empty-text="暂无数据">
        <el-table-column
          type="index"
          width="50">
        </el-table-column>
        <el-table-column
          prop="fdName"
          label="参数名"
          min-width="160">
        </el-table-column>
        <el-table-column
          prop="fdComment"
          label="中文注释"
          min-width="160">
        </el-table-column>
        <el-table-column
          prop="fdType"
          label="类型"
          min-width="160">
        </el-table-column>
      </el-table>
    </el-dialog>
    <div class="headerSearchDiv">
      <el-form :inline="true" class="demo-form-inline headerSearchForm" label-width="68px">
        <el-form-item label="" class="detailTileItem">
          <span class="headerTitle">数据源详细信息：</span>
        </el-form-item>
      </el-form>
      <el-button type="danger" icon="el-icon-delete" class="noOutLine headerButton uploadButton pc" @click="changeSource">删除</el-button>
      <el-button type="danger" icon="el-icon-delete" class="noOutLine headerButton uploadButton mobile" @click="changeSource"></el-button>
    </div>
    <div class="manageDetailDiv">
      <el-form ref="form" :model="source" label-width="90px" class="form-detail">
        <el-form-item label="数据源名：">
          <div class="dbDatabase">
            {{source.dbDatabase}}
          </div>
        </el-form-item>
        <el-form-item label="数据库：">
          <div class="dbType">
            {{source.dbType}}
          </div>
        </el-form-item>
        <el-form-item label="用户名：">
          <div class="dbUser">
            {{source.dbUser}}
          </div>
        </el-form-item>
        <el-form-item label="基本地址：">
          <div class="dbName">
            {{source.dbName}}
          </div>
        </el-form-item>
        <el-form-item label="上传时间：">
          <div class="dbTime">
            {{source.dbTime}}
          </div>
        </el-form-item>
        <el-form-item label="基本表：">
          <div class="formGraphicalDiv">
            <el-table
              :data="tables"
              empty-text="暂无参数"
              class="my-table innerTable">
              <el-table-column
                type="index"
                width="50">
              </el-table-column>
              <el-table-column
                prop="dtName"
                label="表名"
                min-width="160">
              </el-table-column>
              <el-table-column
                label="操作"
                width="150">
                <template slot-scope="scope">
                  <el-button type="success" size="mini" class="noOutLine" @click="seeDetail(scope.row.dtId)">查看详情</el-button>
                </template>
              </el-table-column>
            </el-table>
          </div>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
  import {DELETEDYNAMIC,REQDONEATASOURCE,REQAllTABLE,REQAll} from '../../../../api/types'
  import merge from 'webpack-merge'          //用于动态修改地址栏参数的
  import {mapState,mapActions} from 'vuex'
  export default {
    name: 'manageSourceDetail',
    data () {
      return {
        dbId:'',         //数据源Id

        source:{},       //单一数据源的详细信息
        tables:[],       //单一数据源的基本表
        table:[],        //单一基本表详情


        loading:false,   //是否在获取数据源信息
        loading1:false,  //是否在获取基本表信息
        dialogTableVisible:false
      }
    },
    methods: {
      ...mapActions(['choiceMenuId']),

      //获取一个数据源详细信息
      async reqOneSource(){
        var {dbId} = this
        this.loading = true
        var result = await REQDONEATASOURCE(dbId)
        this.loading = false
        if(result && result.status === 0){
          this.source = result.data
          this.reqTables()
        }else{
          this.message('获取数据源信息失败','error',1500)
        }
      },
      //获取数据源下的基本表
      async reqTables(){
        var {dbId} = this
        var result = await REQAllTABLE(dbId)
        if(result && result.status === 0){
          this.tables = result.data
        }
      },
      //查看基本表详情
      async seeDetail(dtId){
        this.dialogTableVisible = true
        this.loading1 = true
        var result = await REQAll(dtId)
        this.loading1 = false
        if(result && result.status === 0){
          this.table = result.data
        }else{
          this.message('获取基本表信息失败','error',1500)
        }
      },
      //删除数据源
      changeSource(){
        var {dbId} = this
        this.$confirm("确定删除该数据源吗?", '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(async () => {
          this.loading = true
          var result = await DELETEDYNAMIC(dbId)
          this.loading = false
          if(result && result.status === 0){
            this.message('删除成功','success',1000)
            this.$router.replace({name:'SourceList'})
          }else{
            this.message("删除失败",'error',1500)
          }
        }).catch(() => {});
      },

      //消息提示
      message(message,type,duration){
        this.$message({
          message,type,duration
        })
      }
    },
    created () {
      if(this.$route.query.dbId){
        this.dbId = this.$route.query.dbId
        this.choiceMenuId('SourceList')
        this.reqOneSource()
      }else{
        this.$router.replace({name:'SourceList'})
      }
    },
    watch:{
      dialogTableVisible(newval,oldval){
        if(!newval){
          this.table = []
        }
      }
    },
  }
</script>

<style lang="less">
  @import "../../../../assets/css/table/table";

  @import "../../../../assets/css/Item-List/item-list-pc";
  @import "../../../../assets/css/Item-Content/item-content-pc";
  @media screen and (max-width: 767px) {
    @import "../../../../assets/css/Item-List/item-list-mobile";
    @import "../../../../assets/css/Item-Content/item-content-mobile";
  }
</style>
