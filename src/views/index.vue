<template><!-- 实际首页，通过路由关联 --><!-- 要将control的数据传给main -->
  <el-container>
    <el-header>
       <game-control @startGame="clickStartGame"></game-control><!-- 引用方式：组件本身+@开头事件名，事件驱动一个函数clickStartGame -->
    </el-header>
    <el-main>
      <game-main :gameData="gameDataFromIndex"></game-main><!-- gameDataFromIndex数据就给了gameData -->
    </el-main>
  </el-container>
</template>

<script>
import gameControl from '@/views/components/gameControl';
import gameMain from '@/views/components/gameMain';

export default {
  name: 'App',
  data(){//储存数据
    return {
      gameDataFromIndex: {},
    };
  },
  methods: {//储存方法
    clickStartGame(params){//参数params是startGame传过来的数据formData
      console.log(params,123);
      this.gameDataFromIndex = params;//将局部参数params给了全局参数gameDataFromIndex,在main加props实现数据父传子，见main
    },
  },
  components: {//注册组建
    gameControl,
    gameMain,
  },
}
</script>

<style lang="scss">
.el-header{
  display: flex;
  background: #ccc;
  align-items: center;
  justify-content: center;
}
</style>
