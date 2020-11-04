<template>
  <div class="myManage">
    <el-container>
      <el-aside class="mySlider mo" width="220px">
        <Menus></Menus>
      </el-aside>
      <el-container class="myMain">
        <el-header class="myHeader">
          <Nav></Nav>
        </el-header>
        <el-main class="myMainpage">
          <transition name="component-fade" mode="out-in">
            <router-view v-if="!$route.meta.keepAlive" />
          </transition>
          <transition name="component-fade" mode="out-in">
            <keep-alive>
              <router-view v-if="$route.meta.keepAlive" />
            </keep-alive>
          </transition>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
  import Nav from '../../../components/Nav/nav'
  import Menus from '../../../components/Menus/menu'
  import {mapState} from 'vuex'
  export default {
    name: 'manage',
    components: {
      Nav,
      Menus
    },
    computed: {
      ...mapState(['isCollapse'])
    },
    mounted () {},
    watch:{
      isCollapse(newval,oldval){
        if(!newval){
          $('.manage').addClass('moMainpage')
          $('#drawer-bg').removeClass('drawer-bg-show')
          $('.mySlider').removeClass('mo')
          $('.mySlider').removeClass('hidden1')
          $('.myMain').removeClass('toBig')
          $('.mySlider').addClass('open')
          $('.myMain').addClass('toSmall')
        }else{
          $('.manage').removeClass('moMainpage')
          $('.mySlider').removeClass('open')
          $('.mySlider').addClass('mo')
          $('#drawer-bg').addClass('drawer-bg-show')
          $('.myMain').removeClass('toSmall')
          $('.mySlider').addClass('hidden1')
          $('.myMain').addClass('toBig')
        }
      },
    },
  }
</script>

<style scoped lang="less">
  .myManage{
    .drawer-bg-show{
      display: none;
    }
    /*菜单栏样式*/
    .mySlider{
      position: fixed;
      top: 0;
      bottom: 0;
      left: 0;
      z-index: 1001;
      overflow-y: auto;
      overflow-x: hidden;
      width: 220px;
      height: 100%;
      background-color: rgba(0,0,0,0.2);
      font-size: 0;
      -webkit-transition: .4s;
      transition: .4s;
    }
    /*滚动条整体样式*/
    .mySlider::-webkit-scrollbar {
      width: 5px;
      height: 5px;
    }
    /*滚动条里面小方块*/
    .mySlider::-webkit-scrollbar-thumb {
      background: rgb(73,84,100);
      border-radius: 5px;
      -webkit-box-shadow: inset 0 0 0px rgb(50,40,50);
    }
    /*滚动条里面轨道*/
    .mySlider::-webkit-scrollbar-track {
      background: rgba(0,0,0,0.3);
      -webkit-box-shadow: inset 0 0 0px #304156;
      border-radius: 0;
    }
    /*主体样式*/
    .myMain{
      position: relative;
      overflow-x: hidden;
      width: 100%;
      margin-left: 220px;
      -webkit-transition: margin-left .4s;
      transition: margin-left .4s;
      .myHeader{
        overflow: hidden;
        padding: 0;
      }
      .myMainpage{
        padding: 20px;
        color: #FFFFFF;
        min-height: calc(100vh - 60px);
        //去掉滚动条
        &::-webkit-scrollbar {
          width: 0px;
        }
      }
    }
  }
  @media screen and(max-width: 999px){
    .moMainpage{
      position: absolute;
      top: 0;
      bottom: 0;
      left: 0;
      right: 0;
      min-height: 100vh;
    }
    .myManage {
      .mo{
        width: 0px!important;
      }
      .myMain{
        margin: 0!important;
      }
      .open{
        width: 220px!important;
      }
      .mySlider{
        top: 60px!important;
      }
    }
  }
  @media screen and(min-width: 1000px) {
    .myManage{
      .hidden1{
        width: 63px!important;
      }
      .toBig{
        margin-left: 63px!important;
      }
      .open{
        width: 220px!important;
      }
      .toSmall{
        margin-left: 220px!important;
      }
    }
  }
  @media screen and(max-width: 768px) {
    .myManage{
      .myMain{
        .myMainpage{
          padding: 10px;
        }
      }
    }
  }
</style>
