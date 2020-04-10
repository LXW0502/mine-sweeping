<template>
  <div class="game-main" :style="{ width: wh*gameData.width+'px'}" v-if="total && total!=0">
    <span v-for="item in total">
      {{ 如何将item转为x和y }}
    </span>
  </div>
</template>

<script>

export default {
  props: {
    gameData: {//定义一个父从子的变量名
      type: Object,
      default(){
        return {
          width: 0,
          height: 0,
          bombCount: 0,
        }
      }
    },
  },
  data(){//储存数据
    return {
      wh: 25,//一个格子宽高
      arr: [],//二维数组，一个元素对应一个span对象
      /*  
      [
        [{},{},{}],
        [{},{},{}],
        [{},{},{}],
      ]
      */
    };
  },
  methods: {//储存方法

  },
  computed: {//计算属性
    total(){
      return this.gameData.width*this.gameData.height;
    },
  },
  watch: {//监听
    total(newval,oldval){
      var arr = [];
      for(var y=0; y<this.gameData.height; y++){
        arr.push([]);
        for(var x = 0; x<this.gameData.width; x++){
          arr[y].push({
            isOpen: false,//是否翻开,
            value: null,//当前的值,此值isOpen=true三种情况：空值，数字，表示雷；此值isOpen=false两种情况探测，问号

          });
        }
      }
      this.arr = arr;
      console.log(arr)
    },
  },
  components: {//注册组建
    
  },
}
</script>

<style lang="scss">
.game-main{
  display: flex;
  flex-flow: wrap;
  margin: 0 auto;
  span{
    width: 25px;
    height: 25px;
    border: 1px solid #000;
    box-sizing: border-box;
    background: blue;
  }
}
</style>
