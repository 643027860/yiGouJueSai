<template>
  <div class="myMenu">
    <el-menu
      :default-active="menuId"
      class="el-menu-vertical-demo"
      :collapse="isCollapse"
      :unique-opened="true"
      :collapse-transition="false"
      background-color="rgba(0,0,0,0.1)"
      text-color="rgb(255,255,255)"
      active-text-color="rgb(64, 158, 255)"
    >
      <el-submenu index="mainPage">
        <template slot="title">
          <i class="el-icon-house"></i>
          <span slot="title">主页</span>
        </template>
        <el-menu-item-group>
          <router-link :to="{name:'mainPage'}" class="myMenuLink"><el-menu-item index="mainPage" @click="close">主页</el-menu-item></router-link>
        </el-menu-item-group>
      </el-submenu>
      <el-submenu index="SourceList">
        <template slot="title">
          <i class="el-icon-place"></i>
          <span slot="title">数据源</span>
        </template>
        <el-menu-item-group>
          <router-link :to="{name:'SourceList'}" class="myMenuLink"><el-menu-item index="SourceList" @click="close">数据源列表</el-menu-item></router-link>
        </el-menu-item-group>
      </el-submenu>
      <el-submenu index="ModelList">
        <template slot="title">
          <i class="el-icon-office-building"></i>
          <span slot="title">数据模型</span>
        </template>
        <el-menu-item-group>
          <router-link :to="{name:'ModelList'}" class="myMenuLink"><el-menu-item index="ModelList" @click="close">模型列表</el-menu-item></router-link>
        </el-menu-item-group>
        <el-menu-item-group>
          <router-link :to="{name:'UploadModel'}" class="myMenuLink"><el-menu-item index="UploadModel" @click="close">上传模型</el-menu-item></router-link>
        </el-menu-item-group>
      </el-submenu>
      <el-submenu index="ExampleList">
        <template slot="title">
          <i class="el-icon-s-data"></i>
          <span slot="title">数据实例</span>
        </template>
        <el-menu-item-group>
          <router-link :to="{name:'ExampleList'}" class="myMenuLink"><el-menu-item index="ExampleList" @click="close">实例列表</el-menu-item></router-link>
        </el-menu-item-group>
        <el-menu-item-group>
          <router-link :to="{name:'UploadExample'}" class="myMenuLink"><el-menu-item index="UploadExample" @click="close">上传实例</el-menu-item></router-link>
        </el-menu-item-group>
      </el-submenu>
      <el-submenu index="AnalyzeList">
        <template slot="title">
          <i class="el-icon-edit-outline"></i>
          <span slot="title">数据分析</span>
        </template>
        <el-menu-item-group>
          <router-link :to="{name:'AnalyzeList'}" class="myMenuLink"><el-menu-item index="AnalyzeList" @click="close">分析列表</el-menu-item></router-link>
        </el-menu-item-group>
      </el-submenu>
      <el-submenu index="EmbeddedWeb">
        <template slot="title">
          <i class="el-icon-s-promotion"></i>
          <span slot="title">后台服务</span>
        </template>
        <el-menu-item-group>
          <router-link :to="{name:'TracelLog'}" class="myMenuLink"><el-menu-item index="TracelLog" @click="close">日志系统</el-menu-item></router-link>
        </el-menu-item-group>
        <el-menu-item-group>
          <router-link :to="{name:'LinkToTrack'}" class="myMenuLink"><el-menu-item index="LinkToTrack" @click="close">链路追踪</el-menu-item></router-link>
        </el-menu-item-group>
      </el-submenu>
    </el-menu>
  </div>
</template>

<script>
  import {mapState,mapActions} from 'vuex'
  export default {
    computed:{
      ...mapState(['isCollapse','menuId'])
    },
    mounted () {
      if($(window).width()  < 1000) {
        this.ocmenus()
      }
    },
    methods: {
      ...mapActions(['ocmenus']),
      //小于1000px时点击菜单选项隐藏菜单
      close(){
        if($(window).width()  < 1000) {
          this.ocmenus()
        }
      },
    },
  }
</script>

<style lang="less">
  .myMenu{
    .el-menu {
      border-right: none;
      //菜单li
      .el-submenu{
        //有子菜单的父菜单
        .el-submenu__title{
          background-color: rgba(0,0,0,0.1) !important;
          &:hover{
            background-color: rgba(0,0,0,0.3) !important;
          }
        }
        //子菜单
        .el-menu--inline .el-menu-item{
          background-color: rgba(0,0,0,0.2) !important;
          &:hover{
            background-color: rgba(0,0,0,0.3) !important;
            color: rgb(64, 158, 255) !important;
          }
        }
        //菜单之间的间距
        .el-menu-item-group__title{
          padding-bottom: 0px;
          padding-top: 0px;
        }
        //router-link样式
        .myMenuLink{
          text-decoration: none;
        }
        &:hover{
          background-color: rgba(0,0,0,0.1) !important;
        }
      }
    }
  }
</style>
