<template>
  <div class="manageItemList manageItemDetail manageAnalyzeDetail"
       v-loading="loading"
       element-loading-text="数据加载中"
       element-loading-spinner="el-icon-loading"
       element-loading-background="rgba(0, 0, 0, 0.1)">
    <div class="headerSearchDiv">
      <el-form :inline="true" class="demo-form-inline headerSearchForm" label-width="68px">
        <el-form-item label="" class="detailTileItem">
          <span class="headerTitle">数据分析详情：</span>
        </el-form-item>
      </el-form>
    </div>
    <div class="manageDetailDiv">
      <el-form ref="form" :model="ae" label-width="90px" class="form-detail">
        <el-form-item label="分析名称：">
          {{ae.aeName}}
        </el-form-item>
        <el-form-item label="实例数据：">
          <a class="myFileLink" :href="qu.quUrl" v-if="qu && qu.quUrl">数据集.xlsx</a>
        </el-form-item>
        <el-form-item label="分析时间：">
          {{formatTime(ae.aeTime)}}
        </el-form-item>
        <el-form-item label="分析结果：">
          {{ae.aeResult}}
        </el-form-item>
        <el-form-item label="数据模型：" v-if="model.status !== 'error'">
          <el-collapse v-model="activeNames" class="formGraphicalDiv">
            <el-collapse-item title="" name="0">
              <div class="modelForm emptyModelForm" v-if="model.status === 'loading'"
                   v-loading="true"
                   element-loading-text="获取模型数据中"
                   element-loading-spinner="el-icon-loading"
                   element-loading-background="rgba(0, 0, 0, 0.3)">
              </div>
              <el-form ref="form" v-if="model.status === 'success'" :model="model" label-width="90px" class="modelForm">
                <el-form-item label="模型名称：">
                  {{model.mlName}}
                </el-form-item>
                <el-form-item label="模型算法：">
                  {{model.mlAlgo}}
                </el-form-item>
                <el-form-item label="模型功能：">
                  {{model.mlResult}}
                </el-form-item>
                <el-form-item label="模型介绍：">
                  {{model.mlIntro}}
                </el-form-item>
                <el-form-item label="模型参数：" class="formInnerTable">
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
            </el-collapse-item>
          </el-collapse>
        </el-form-item>
        <el-form-item label="分析实例：">
          <div class="graphicalDiv formGraphicalDiv" v-for="(myData,index) in myDatas" :key="index">
            <div v-if="!myData.loading">
              <div class="titleDiv dataTitleDiv" >
                <p class="titleP" :id="index + '-' + myData.id">
                  {{myData.data.quName}}
                </p>
              </div>
              <div class="contentDiv">
                <MyLine :MyLineId="myData.id" :myData="myData" v-if="myData.chartType=='折线图'"></MyLine>
                <MyPie :myPieId="myData.id" :myData="myData" v-if="myData.chartType=='饼图'"></MyPie>
                <MyBar :MyBarId="myData.id" :myData="myData" v-if="myData.chartType=='柱形图'"></MyBar>
              </div>
            </div>
            <div v-else
                 style="height: 100%"
                 v-loading="myData.loading"
                 element-loading-text="数据拼命拉取中"
                 element-loading-spinner="el-icon-loading"
                 element-loading-background="rgba(0, 0, 0, 0)">
            </div>
          </div>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
  import MyLine from '../../../../components/ChangeCharts/myLine'
  import MyPie from '../../../../components/ChangeCharts/myPie'
  import MyBar from '../../../../components/ChangeCharts/myBar'
  import {REQALLANALYZE,REQONEINTER,REQONEMODEL} from '../../../../api/types'
  import merge from 'webpack-merge'          //用于动态修改地址栏参数的
  import {mapState,mapActions} from 'vuex'
  export default {
    name: 'manageModelDetail',
    data () {
      return {
        ae:{},            //分析对象
        aeId:'',          //分析Id

        myDatas:[],       //实例对象（图表）数组

        activeNames:['0'],//模型收缩框
        model:{},         //单一模型的详细信息

        qu:{},            //实例对象

        pageNum: 1,
        pageSize: 100,
        showFlag: 1,     //显示标志，1为显示，0为不显示

        loading:false,
      }
    },
    components:{
      MyLine,MyPie,MyBar
    },
    methods: {
      ...mapActions(['choiceMenuId']),

      //获取大数据分析列表
      async reqAnalyzes(){
        var pageNum = 1
        var pageSize = 10000
        var {showFlag,aeId} = this
        this.loading = true
        var result = await REQALLANALYZE({pageNum,pageSize},showFlag)
        this.loading = false
        if(result && result.status === 0){
          var analyzeList = result.data.analyzeListVOS
          this.ae = analyzeList.find(item => item.aeId == aeId)
          if(this.ae.aeId){
            //获取到分析对象，再获取实例图表
            this.reqExamplae(this.ae.quId)
            //获取到分析对象，再获取数据模型
            this.reqOneModel(this.ae.mlId)
          }else{
            //获取不到分析对象，提示
            this.message('暂无数据','warning',1500)
            setTimeout(()=>{
              this.$router.replace({name:'AnalyzeList'})
            },1500)
          }
        }else{
          if(result.msg){
            var message = result.msg
          }else{
            var message = '获取数据分析失败'
          }
          this.message(message,'error',1500)
        }
      },

      //获取一个大数据模型的详细信息
      async reqOneModel(mlId){
        this.model = {status:'loading'}
        var result = await REQONEMODEL(mlId)
        if(result && result.status === 0){
          this.model = result.data
          this.model.status = 'success'
        }else{
          this.model = {status:'error'}
        }
      },
      //查询实例，生成图表
      async reqExamplae(quId){
        this.myDatas.push({loading:true})                                           //为了显示拉取数据中而加入的空数据
        var result = await REQONEINTER(quId)
        this.qu = result.data                                                       //实例对象
        this.myDatas = this.myDatas.filter(myData => !myData.loading)               //去掉为了显示拉取数据中的空数据
        var id = new Date().getTime() + ''                                          //用于标识该表ID
        if(result && result.status == 0){
          //获取数据之后进行处理，横坐标放X轴，纵坐标放Y轴,y轴属性名放yNameData，x轴属性名放xNameData,chartType图表类型,quId列表查询ID,id改图标的id,data包含所有返回数据
          var myData = {data:result.data,x:[],y:[],yNameData:[],xNameData:[],chartType:'',quId,id}
          var qsVOMap = result.data.qsVOMap
          Object.keys(qsVOMap).forEach(key => {
            var isX = false
            myData.chartType = qsVOMap[key].chartType
            if(qsVOMap[key].chartOption == '横轴'){
              isX = true
            }
            if(!isX){
              myData.y.push(qsVOMap[key].fieldValues)
              myData.yNameData.push(key)
            }else{
              myData.x.push(qsVOMap[key].fieldValues)
              myData.xNameData.push(key)
            }
          })
          this.myDatas.push(myData)
        }else{
          if(result.msg){
            var message = result.msg
          }else {
            var message = '查询实例失败'
          }
          this.message(message,'error',1500)
        }
      },
      //返回规定格式的时间
      formatTime(time){
        if(time){
          return time.slice(0,10) + '    '+ time.slice(11,19)
        }
      },
      //消息提示
      message(message,type,duration){
        this.$message({
          message,type,duration
        })
      }
    },

    created () {
      if(this.$route.query.aeId){
        this.aeId = this.$route.query.aeId
        this.showFlag = this.$route.query.showFlag
        this.choiceMenuId('AnalyzeList')
        //获取数据分析列表
        this.reqAnalyzes()
      }else{
        this.$router.replace({name:'AnalyzeList'})
      }
    },
  }
</script>

<style lang="less">
  @import "../../../../assets/css/el-collapse/el-collapse";
  @import "../../../../assets/css/table/table";
  @import "../../../../assets/css/input/input";

  @import "../../../../assets/css/Item-List/item-list-pc";
  @import "../../../../assets/css/Item-Content/item-content-pc";
  @media screen and (max-width: 767px) {
    @import "../../../../assets/css/Item-List/item-list-mobile";
    @import "../../../../assets/css/Item-Content/item-content-mobile";
  }
</style>
