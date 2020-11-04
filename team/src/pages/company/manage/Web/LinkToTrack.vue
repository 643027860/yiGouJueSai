<template>
  <div class="embeddedWeb">
    <iframe :src="path" class="web" width="100%" frameborder="0"></iframe>
  </div>
</template>

<script>
  import {mapActions} from 'vuex'
  export default {
    name: 'LinkToTrack',
    data() {
      return {
        path:'http://192.168.3.7:8213/wallboard',    //嵌入网页地址
      }
    },
    methods:{
      ...mapActions(['choiceMenuId']),
    },
    mounted () {
      this.choiceMenuId('LinkToTrack')
    }
  }
</script>

<style lang="less">
  .embeddedWeb{
    .web{
      min-height: calc(100vh - 105px);
    }
  }
</style>
