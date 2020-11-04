<template>
  <div class="errorDiv clearFloat">
    <el-row :gutter="10">
      <el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12">
        <div class="imgDiv">
          <img src="./404.jpg" alt="">
        </div>
      </el-col>
      <el-col :xs="24" :sm="12" :md="12" :lg="12" :xl="12" >
        <div class="fontDiv">
          <h1 class="errorH1">404</h1>
          <h2 class="errorH2">哦！页面丢失</h2>
          <p class="errorP">您所寻找的页面不存在。您可以点击下面的按钮，返回登陆。</p>
          <button class="errorButton" @click="$router.replace({name:'login'})">
            返回首页
          </button>
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  export default {}
</script>

<style lang="less">
  .errorDiv{
    overflow-x: hidden;
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: 150px auto 0;
    color:rgb(14,6,32);
    .imgDiv{
      float: right;
      width: 500px;
      text-align: center;
      img{
        width: 320px;
        height: 320px;
      }
    }
    .fontDiv{
      max-width: 300px;
      .errorH1{
        margin: 10px 0 0;
        font-size: 120px;
        font-weight: bold;
      }
      .errorH2{
        margin: 20px 0;
        font-size: 32px;
        font-weight: bold;
      }
      .errorP{
        font-size: 16px;
      }
      .errorButton{
        position: relative;
        overflow: hidden;
        z-index: 1;
        margin: 5px 0px;
        padding: 8px 50px;
        background: transparent;
        font-size: 1.5em;
        border-radius: 30px;
        border: 4px solid rgb(44,207,109);
        cursor: pointer;
        letter-spacing: 2px;
        font-weight: bold;
        outline:none;
        transition: 0.2s ease;
        -webkit-transition: 0.2s ease;
        &:before{
          position: absolute;
          z-index: -1;
          left: 0;
          top: 0;
          width: 0%;
          height: 100%;
          background-color: rgb(44,207,109);
          content: "";
          cursor: pointer;
          -webkit-transition: 0.2s ease;
          transition: 0.2s ease;
        }
        &:hover::before{
          width: 100%;
        }
      }
    }
  }
  @media screen and (max-width: 767px){
    .errorDiv {
      margin-top: 50px;
      .imgDiv{
        width: 100%;
        img{
          margin: auto;
          width: 200px;
          height: 200px;
        }
      }
      .fontDiv{
        width: 100%;
        margin: auto;
        text-align: center;
        .errorH1{
          font-size: 80px;
        }
      }
    }
  }
  @media screen and (max-width: 499px){
    .errorDiv{
      .fontDiv{
        width: 90% !important;
        margin: 20px auto 0;
      }
      .errorH1{
        font-size: 45px;
      }
      .errorH2{
        font-size: 30px;
      }
    }
  }
</style>
