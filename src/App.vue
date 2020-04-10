<template><!-- 入口页面 -->
  <div class="app" @contextmenu.prevent><!-- js禁止右键菜单  事件加修饰符 -->
    <router-view />
  </div>
</template>

<script>

export default {
  name: 'App',
  data(){//储存数据
    return {

    };
  },
  methods: {//储存方法

  },
  components: {//注册组建

  },
}
</script>

<style lang="scss">
@import './assets/font-awesome/css/font-awesome.min.css';//引入字体图标库
*{ margin: 0; padding: 0;}
html,body,.app,.el-container{ height: 100%;}
</style>
