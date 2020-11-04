<template>
  <div class="myNavIcon myAvatar">
    <el-dropdown @command="handleCommand" trigger="click">
      <span class="el-dropdown-link">
        <div class="myNavInner">
          <img class="noSelect myNavImg" src="https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=1463578878,1262482751&fm=26&gp=0.jpg" alt="">
          <span class="myNavSpan">{{user.name||'未登录'}}</span>
          <i class="el-icon-caret-bottom myNavI"></i>
        </div>
      </span>
      <el-dropdown-menu slot="dropdown">
        <el-dropdown-item divided command="out">退出登录</el-dropdown-item>
      </el-dropdown-menu>
    </el-dropdown>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        user:{}
      }
    },
    mounted () {
      var user = JSON.parse(localStorage.getItem('RUANJIANBEI_USER'))
      if(user && user.user){
        this.user = user.user.sysUser
      }
    },
    methods: {
      handleCommand(command){
        if(command == 'out'){
          this.$confirm("确定退出?", '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(async () => {
            this.$router.replace({name:'login'})
            localStorage.setItem('RUANJIANBEI_USER',JSON.stringify(null))
          }).catch(() => {});
        }
      },
    },
  }
</script>

<style lang="less">
  //解决hover影响移动端
  @media (any-hover: hover){
    .myNavIcon:hover{
      background-color: transparent;
      cursor:pointer;
    }
  }
  .el-dropdown-menu{
    padding: 0!important;
    margin-top: 0!important;
  }
  .el-dropdown-menu__item--divided:before{
    height: 0px!important;
  }
  .el-dropdown-menu__item--divided{
    margin-top: 0;
  }
  .myAvatar{
    float: right;
    padding: 0 8px;
    margin-right: 30px;
    .myNavInner{
      position: relative;
      vertical-align: middle;
      .myNavImg{
        width: 30px;
        height: 30px;
        border-radius: 10%;
        vertical-align: middle;
        cursor: pointer;
      }
      .myNavSpan{
        display: inline-block;
        padding: 5px 10px;
        color: #FFFFFF;
        font-size: 15px;
        line-height: 30px;
        vertical-align: middle;
      }
      .myNavI{
        position: absolute;
        right: -20px;
        top: 25px;
        color: #FFFFFF;
        font-size: 12px;
        cursor: pointer;
      }
    }
  }
</style>
