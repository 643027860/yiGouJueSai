<template>
  <div>
    <router-view></router-view>
  </div>
</template>

<script>
export default {}
</script>

<style lang="less">
  /*引入基本css*/
  @import '../src/assets/css/base.less';
  @import '../src/assets/css/transition.less';
  body,html{
    padding: 0;
    margin: 0;
    /*滚动条整体样式*/
    &::-webkit-scrollbar {
      width: 0px;
    }
  }
  //去掉遮挡层出现时默认给body加的17排序padding-left
  .el-popup-parent--hidden{
    padding: 0!important;
  }
  /*适配pc和手机的表单宽度*/
  .pmFormWidth{
    width: 60% !important;
  }
  //修改平板和移动端的消息提示样式以及是否可见,弹出窗样式
  @media screen and (max-width: 768px) {
    .pc{
      display: none!important;
    }
    .mobile{
      display: block!important;
    }
    .el-dialog__wrapper .el-dialog {
      width: 300px !important;
      .el-dialog__body{
        padding: 10px 20px!important;
        .el-form-item__label{
          width: 68px!important;
        }
        .el-select,.el-input{
          width: 180px!important;
        }
      }
    }
    .el-message {
      min-width: 300px !important;
    }
    .el-message-box{
      width: 300px !important;
    }
    /*适配pc和手机的表单宽度*/
    .pmFormWidth{
      width: 100% !important;
    }
  }
</style>
