<template>
  <div class="embeddedWeb">
    <iframe :src="path" class="web" width="100%" frameborder="0"></iframe>
  </div>
</template>

<script>
  import {mapActions} from 'vuex'
  export default {
    name: 'TracelLog',
    data() {
      return {
        path:' http://192.168.3.7:5601/app/kibana#/discover',    //嵌入网页地址
      }
    },
    methods:{
      ...mapActions(['choiceMenuId']),
    },
    mounted () {
      this.choiceMenuId('TracelLog')
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
