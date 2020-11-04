<template>
  <div class="myNav clearFloat">
    <MenuControl style="float: left;"></MenuControl>
    <div class="rightAvatar clearFloat">
      <Avatar></Avatar>
    </div>
  </div>
</template>

<script>
  import Avatar from './avatar'
  import MenuControl from '../Menus/controlmenu'
  export default {
    components: {
      Avatar,
      MenuControl
    },
  }
</script>

<style lang="less">
  .myNav{
    height: 60px;
    background-color: rgba(0,0,0,0.3);
    .rightAvatar{
      float: right;
      padding: 10px 0;
    }
  }
</style>
