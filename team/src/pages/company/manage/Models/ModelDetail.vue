<template>
  <div class="manageItemList manageItemDetail manageModelDetail"
       v-loading="loading"
       element-loading-text="数据加载中"
       element-loading-spinner="el-icon-loading"
       element-loading-background="rgba(0, 0, 0, 0.1)">
    <div class="headerSearchDiv">
      <el-form :inline="true" class="demo-form-inline headerSearchForm" label-width="68px">
        <el-form-item label="" class="detailTileItem">
          <span class="headerTitle">数据模型详情：</span>
        </el-form-item>
      </el-form>
      <div class="uploadButton">
        <el-button type="primary" icon="el-icon-edit" class="noOutLine headerButton pc" v-if="showFlag == 1 && !edit" @click="toEdit()">修改</el-button>
        <el-button type="primary" icon="el-icon-edit" class="noOutLine headerButton pc" v-if="showFlag == 1 && edit" @click="edit = false">取消</el-button>
        <el-button type="danger" icon="el-icon-delete" class="noOutLine headerButton pc" v-if="showFlag == 1" @click="changeModel(0)">删除</el-button>
        <el-button type="primary" icon="el-icon-refresh-left" class="noOutLine headerButton pc" v-if="showFlag == 0" @click="changeModel(1)">恢复</el-button>
      </div>
      <div class="uploadButton">
        <el-button type="primary" icon="el-icon-edit" class="noOutLine headerButton mobile" v-if="showFlag == 1 && !edit" @click="edit = true"></el-button>
        <el-button type="primary" icon="el-icon-edit" class="noOutLine headerButton mobile" v-if="showFlag == 1 && edit" @click="edit = false">取消</el-button>
        <el-button type="danger" icon="el-icon-delete" class="noOutLine headerButton mobile" v-if="showFlag == 1" @click="changeModel(0)"></el-button>
        <el-button type="primary" icon="el-icon-refresh-left" class="noOutLine headerButton mobile" v-if="showFlag == 0" @click="changeModel(1)"></el-button>
      </div>
    </div>
    <div class="manageDetailDiv">
      <el-form ref="form" :model="updateForm" label-width="90px" class="form-detail uploadModelTable" v-show="edit">
        <el-form-item label="模型算法：">
          <el-select v-model="updateForm.mlAlgo" placeholder="请选择">
            <el-option label="Python" value="Python"></el-option>
            <el-option label="R" value="R"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="模型名称：">
          <el-input v-model="updateForm.mlName"></el-input>
        </el-form-item>
        <el-form-item label="模型功能：">
          <el-input
            type="textarea"
            :autosize="{ minRows: 2, maxRows: 4}"
            placeholder="请输入模型功能"
            v-model="updateForm.mlResult">
          </el-input>
        </el-form-item>
        <el-form-item label="模型介绍：">
          <el-input
            type="textarea"
            :autosize="{ minRows: 4, maxRows: 8}"
            placeholder="请输入内容"
            v-model="updateForm.mlIntro">
          </el-input>
        </el-form-item>
        <el-form-item label="模型参数：">
          <div class="formGraphicalDiv">
            <el-table
              :data="updateForm.modelSmallPutDTOS"
              empty-text="暂无参数"
              class="my-table innerTable paramsTable">
              <el-table-column
                type="index"
                width="50">
              </el-table-column>
              <el-table-column
                label="参数名"
                min-width="120">
                <template slot-scope="scope">
                  <el-input placeholder="请输入参数名" v-model="updateForm.modelSmallPutDTOS[scope.$index].msName">{{scope.row.msName}}</el-input>
                </template>
              </el-table-column>
              <el-table-column
                label="参数描述"
                min-width="180">
                <template slot-scope="scope">
                  <el-input placeholder="请输入参数描述" v-model="updateForm.modelSmallPutDTOS[scope.$index].msIntro">{{scope.row.msIntro}}</el-input>
                </template>
              </el-table-column>
              <el-table-column
                label="操作"
                width="200">
                <template slot-scope="scope">
                  <el-button type="danger" size="mini" @click="removeDTOS(scope.$index)">删除</el-button>
                  <el-button type="primary" size="mini" v-if="scope.$index == updateForm.modelSmallPutDTOS.length-1" @click="addDTOS()">继续添加</el-button>
                </template>
              </el-table-column>
            </el-table>
          </div>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" class="noOutLine" @click="updateModel()">立即修改</el-button>
          <el-button class="noOutLine" @click="edit = false">取消</el-button>
        </el-form-item>
      </el-form>
      <el-form ref="form1" :model="model" label-width="90px" class="form-detail" v-show="!edit">
        <el-form-item label="模型算法：">
          <div class="pmFormWidth">
            {{model.mlAlgo}}
          </div>
        </el-form-item>
        <el-form-item label="模型名称：">
          <div class="pmFormWidth">
            {{model.mlName}}
          </div>
        </el-form-item>
        <el-form-item label="模型功能：">
          <div class="pmFormWidth">
            {{model.mlResult}}
          </div>
        </el-form-item>
        <el-form-item label="模型介绍：">
         <div class="pmFormWidth">
           {{model.mlIntro}}
         </div>
        </el-form-item>
        <el-form-item label="模型参数：">
          <div class="formGraphicalDiv">
            <el-table
              :data="model.modelSmallVOS"
              empty-text="暂无参数"
              class="my-table innerTable">
              <el-table-column
                type="index"
                width="50">
              </el-table-column>
              <el-table-column
                prop="msName"
                label="参数名"
                min-width="120">
              </el-table-column>
              <el-table-column
                prop="msIntro"
                label="参数描述"
                min-width="180">
              </el-table-column>
            </el-table>
          </div>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
  import {REQONEMODEL,UPLOADONEMODEL,UPDATEONEMODEL} from '../../../../api/types'
  import merge from 'webpack-merge'          //用于动态修改地址栏参数的
  import {mapState,mapActions} from 'vuex'
  export default {
    name: 'manageModelDetail',
    data () {
      return {
        mlId:'',         //模型Id
        edit:false,      //是否可修改
        pageNum: 1,
        pageSize: 100,
        showFlag: 1,     //显示标志，1为显示，0为不显示

        model:{},        //单一模型的详细信息
        modelCanShu:[],  //单一模型的参数
        modelList:[],    //模型数据列表

        dialogUploadModelVisible:false,  //弹出上传模型窗口
        //修改模型表单
        updateForm:{
          mlAlgo:'',
          mlIntro:'',
          mlName:'',
          mlResult:'',
          modelSmallPutDTOS:[],
        },

        loading:false,
      }
    },
    methods: {
      ...mapActions(['choiceMenuId']),

      //获取一个大数据模型的详细信息
      async reqOneModel(){
        var {mlId} = this
        this.loading = true
        var result = await REQONEMODEL(mlId)
        this.loading = false
        if(result && result.status === 0){
          this.model = result.data
        }
      },

      //点击修改模型,修改当前显示状态edit以及赋值给修改表单
      toEdit(){
        this.edit = true
        this.fuZhi()
      },
      //将模型信息赋值给修改表单
      fuZhi(){
        var {mlAlgo,mlIntro,mlName,showFlag,mlResult,modelSmallVOS} = this.model
        this.updateForm.mlAlgo = mlAlgo
        this.updateForm.mlIntro = mlIntro
        this.updateForm.mlName = mlName
        this.updateForm.showFlag = showFlag
        this.updateForm.mlResult = mlResult
        this.updateForm.modelSmallPutDTOS = []
        modelSmallVOS.map(item => {
          this.updateForm.modelSmallPutDTOS.push({msIntro:item.msIntro,msName:item.msName})
        })
      },
      //添加上传模型参数
      addDTOS(){
        this.updateForm.modelSmallPutDTOS.push({
          msIntro:'',
          msName:'',
        })
      },
      //删除上传模型参数
      removeDTOS(index){
        var {modelSmallPutDTOS} = this.updateForm
        if(modelSmallPutDTOS.length > 1){
          this.updateForm.modelSmallPutDTOS = modelSmallPutDTOS.filter((item,index1) => index1 !== index)
        }else{
          this.updateForm.modelSmallPutDTOS = [{
            msIntro:'',
            msName:'',
          }]
        }
      },
      //修改大数据模型状态,showFlag为0时，删除该模型；为1时，恢复该模型。
      changeModel(showFlag = 1){
        this.$confirm(`确定${showFlag?'恢复':'删除'}该模型吗?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(async () => {
          var {mlId,model} = this
          var {mlAlgo,mlIntro,mlName,mlResult,modelSmallVOS} = model
          var modelSmallPutDTOS = modelSmallVOS
          var params = {mlAlgo,mlIntro,mlName,mlResult,modelSmallPutDTOS}
          params.showFlag = showFlag
          this.loading = true
          var result = await UPDATEONEMODEL(params,mlId)
          this.loading = false
          if(result && result.status === 0){
            this.message(showFlag === 0?'删除成功':'恢复成功','success',1500)
            this.showFlag = showFlag
            this.chanceUrlQuery({mlId,showFlag})
          }else{
            this.message(showFlag === 0?'删除失败':'恢复失败','error',1500)
          }
        }).catch(() => {});
      },

      //修改大数据模型
      async updateModel(){
        var {mlAlgo,mlIntro,mlName,mlResult,modelSmallPutDTOS} = this.updateForm
        //过滤掉没填写完整信息的参数
        modelSmallPutDTOS = modelSmallPutDTOS.filter(item => {
          if(item.msName.trim() && item.msIntro.trim()){
            return item
          }
        })
        if(mlAlgo && mlIntro && mlName && mlResult && modelSmallPutDTOS.length > 0){
          var {mlId,showFlag} = this
          var modelPutDTO  = {mlAlgo,mlIntro,mlName,mlResult,modelSmallPutDTOS,showFlag}
          this.loading = true
          var result = await UPDATEONEMODEL(modelPutDTO,mlId)
          this.loading = false
          if(result && result.status === 0){
            //修改成功后需要重置更新表单并获取新的模型数据和取消当前修改状态
            this.emptyForm()
            this.edit = false
            this.reqOneModel()
            this.message('修改成功','success',1500)
          }else{
            this.message('修改失败','error',1500)
          }
        }else{
          this.message('请输入完整模型信息','warning',1500)
        }
      },
      //修改路由参数
      chanceUrlQuery(data){
        //清空所有的参数：
        this.$router.push({
          query:merge({},{})
        })
        //更新路由参数
        this.$router.push({
          query:merge(this.$route.query,data)
        })
      },
      //消息提示
      message(message,type,duration){
        this.$message({
          message,type,duration
        })
      }
    },
    created () {
      if(this.$route.query.mlId){
        this.mlId = this.$route.query.mlId
        this.showFlag = this.$route.query.showFlag
        this.choiceMenuId('ModelList')
        this.reqOneModel()
      }else{
        this.$router.replace({name:'ModelList'})
      }
    },
  }
</script>

<style lang="less">
  @import "../../../../assets/css/table/table";
  @import "../../../../assets/css/input/input";

  @import "../../../../assets/css/Item-List/item-list-pc";
  @import "../../../../assets/css/Item-Content/item-content-pc";
  @media screen and (max-width: 767px) {
    @import "../../../../assets/css/Item-List/item-list-mobile";
    @import "../../../../assets/css/Item-Content/item-content-mobile";
  }
</style>
