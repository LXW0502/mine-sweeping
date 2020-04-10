<template><!-- 翻开三种情况：空白 1，数字 2，雷 3；未翻开三种情况：蓝空白 4，探测(旗) 5，问号 6   默认蓝色空白 -->
  <div class="game-main" :style="{ width: wh*gameData.width+'px'}" v-if="total && total!=0 && arr.length != 0">
    <span v-for="item in total" :key="item" :class="{ blue: coordinateSpan(item).type == 4, white: (coordinateSpan(item).type == 1 || coordinateSpan(item).type == 2  || coordinateSpan(item).type == 3) }"
    @mousedown="mousedownFun($event,coordinateSpan(item))">
    <!-- $event是事件对象，coordinateSpan(item))是当前这块对应的数据对象 -->
    <!--加:class，判断什么时候要加blue，total表示有几个格子，arr是一个二维数组，希望给个span对应一个对象，arr里面是存的每个格子的对象，要将对象对应到span中，需要将total数字转化为一个二维坐标 -->
    <!-- coordinateSpan(item)函数返回的是对象this.arr[y][x];对象又有属性type -->
    <i class="el-icon-setting bomb" v-if="coordinateSpan(item).type == 3"></i><!-- 雷 -->
    <i class="fa fa-flag flag" v-if="coordinateSpan(item).type == 5"></i><!-- 旗子 -->
    <i class="fa fa-question question" v-if="coordinateSpan(item).type == 6"></i><!-- 问号 -->
    {{coordinateSpan(item).type == 2 ? coordinateSpan(item).value : ''}}<!--显示数字， 如果当前为2，则显示其值，否则为空白 -->
    </span>
  </div>
</template>

<script>

export default {
  props: {//props将父的数据传给子，在组建game-main上得到数据，见index
    gameData: {//定义一个父传子的变量名，里面的数据只能从父得到，不能在当前组件main中修改，监听可以
      type: Object,//定义类型json对象类型
      default(){
        return {//control里的值最终存到这里
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
        [{},{},{}],//第一行的每个单元
        [{},{},{}],
        [{},{},{}],
      ]
      */
    };
  },
  methods: {//储存方法
    coordinateSpan(item){//将当前坐标的span对应的数据对象返回
      var x = (item-1) % this.gameData.width;//得到x坐标
      var y = Math.floor((item-1) / this.gameData.width);//得到x坐标,下舍
      return this.arr[y][x];//得到span的当前对象
    },
    mousedownFun(e,data){//e表示事件对象;data表示当前span的数据对象
      console.log(e);
      var button = e.button;//左键：非IE返回0，IE返回1；右键：返回2
      if(button <= 1){//左键（翻开动作）
        var x = data.x;
        var y = data.y;

        this.recursionFun({
          x,
          y
        }, true)
      }
      else if(button == 2){//右键（未翻开动作）,探测（旗子）、问号
        //只有type不等于1,2,3的时候才能进行切换
        //作业 - 切换 探测（旗）， 问号， 蓝空白
      }
    },
    recursionFun(coordinate, bool){
    //coordinate坐标是个对象，有xy，coordinate表示坐标，此函数作用，对当前span做个判断，并对其周围的span判断
    //bool表示是否是鼠标点击的当前span数据对象
      var x = coordinate.x;
      var y = coordinate.y;
      var data = this.arr[y][x];
      if(data.type == 4 || data.type == 6){//判断是否能被翻开,只有当前是蓝空白，或者问号才能被翻开

        if(bool){//如果是当前点击的span
          console.log(data.isBomb,`x:${x},y:${y}`)

          if(data.isBomb){//如是雷    要拼在一起的话，bool放在这个（）里面，else放重复的东西
            data.type = 3;
            //将所有的雷显示出来
            //
            //
            
            return;
          }
          else{//判断周边八个是否有雷
            var count = this.countFun(x,y);
            if(count == 0){//雷数为0 ，当前空白1
              data.type = 1;
              //周边8个span
              for(var c_x = x - 1; c_x <= x + 1; c_x++){
                for(var c_y = y -1; c_y <= y + 1; c_y++){
                  //console.log(data.isBomb,`c_x:${x},c_y:${y}`)
                  if(c_x == x && c_y == y){//跳过此次循环进入下次循环
                    continue;
                  }
                  else if(c_x >= 0 && c_y >= 0 && this.arr[c_y] != null && this.arr[c_y][c_x] != null && this.arr[c_y][c_x].isBomb == false){//要保证[c_y][c_x]大于0 this.arr[c_y]存在，并且不包括x ，y
                    this.recursionFun({//当前是空白，要递归周围的8个，递归周边的8个
                      x: c_x,
                      y: c_y,//不写bool，bool表示鼠标点击的当前值，每点击，所以不用加
                    })
                  }
                }
              }
              
            }
            else{
              data.type = 2;//数字 2，
              data.value = count;
            }
          }
        }


        else{//如果是span周边的8个
          var count = this.countFun(x,y);//先判断当前周边有没有雷
          if(count == 0){//雷数为0 ，当前空白1
            data.type = 1;
            //周边8个span
            for(var c_x = x - 1; c_x <= x + 1; c_x++){
              for(var c_y = y -1; c_y <= y + 1; c_y++){
                //console.log(`c_x:${c_x},c_y:${c_y}`,22)
                if( (c_x != x || c_y != y) && c_x >= 0 && c_y >= 0 && this.arr[c_y] != null && this.arr[c_y][c_x] != null && this.arr[c_y][c_x].isBomb == false){//要保证[c_y][c_x]大于0 this.arr[c_y]存在，并且不包括x ，y
                   this.recursionFun({//当前是空白，要递归周围的8个，递归周边的8个
                    x: c_x,
                    y: c_y,//不写bool，bool表示鼠标点击的当前值，每点击，所以不用加
                  })
                }
              }
            }
            
          }
          else{
            data.type = 2;//数字 2，
            data.value = count;
          }

        }
      }
    },
    countFun(x,y){//统计当前x,y周边8个span的雷数量
      var count = 0;
      for(var c_x = x - 1; c_x <= x + 1; c_x++){
        for(var c_y = y -1; c_y <= y + 1; c_y++){
          if( (c_x != x || c_y != y) && c_x >= 0 && c_y >= 0 && this.arr[c_y] != null && this.arr[c_y][c_x] != null && this.arr[c_y][c_x].isBomb){//要保证[c_y][c_x]大于0 this.arr[c_y]存在，并且不包括x ，y
            console.log(`c_x:${c_x},c_y:${c_y}`,33)
            count++;
          }
        }
      }
      return count;
    },
  },
  computed: {//计算属性，不能重新赋值，可计算，可通过this.gameData.width*this.gameData.height;得到，属性发生变化时会变化
    total(){//总的方块数
      return this.gameData.width*this.gameData.height;
    },
  },
  watch: {//监听  total发生变化就会触发
    total(newval,oldval){//将每个格子变一个数据对象，二维数据 
      var arr = [];
      //两个for，使每个span对应到每个数据
      for(var y=0; y<this.gameData.height; y++){
        arr.push([]);
        for(var x = 0; x<this.gameData.width; x++){
          arr[y].push({
            type: 4,//当前的类型,翻开三种情况：空白 1，数字 2，雷 3；未翻开三种情况：蓝空白 4，探测(旗) 5，问号 6   默认蓝色空白
            value: null,//值，只有type==2的时候才有此值，表示周边雷的数量
            isBomb: false,//是否是雷，埋雷
            x,//当前数据所在的坐标
            y,//当前数据所在的坐标
          });//返回的是对象type value ...
        }
      }
      //通过随机，放雷
      var coordinateArr =[];//所有坐标的数组，位置不能重复     i是从0开始的
      for(var i = 0; i < newval; i++){//设计所有位置的雷的坐标，newval是total的新值
        var x = i % this.gameData.width;//得到x坐标
        var y = Math.floor( i / this.gameData.width);//得到x坐标,下舍
        coordinateArr.push({ x, y });//默认false
      }
      var coordinateBombArr = [];//记录有雷的坐标的数组
      for(var j = 0; j < this.gameData.bombCount; j++){//随机放雷,数量与bombCount有关
        var pos = Math.floor(coordinateArr.length * Math.random());//得到随机的位置0-coordinateArr.length-1

        var x = coordinateArr[pos].x;
        var y = coordinateArr[pos].y;
        //coordinateBombArr.push(coordinateArr[pos]);//循环里就可以标记了
        //push 后删除当前位置
        coordinateArr.splice(pos,1);//删除包括位置pos，删除个数1   

        arr[y][x].isBomb = true;//将对应坐标的数据标记为有雷
      }




      this.arr = arr;
      console.log(arr);//看整个数组是不是对的
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
    //雷居中
    display: flex;
    justify-content:center;//水平居中
    align-items:center;//垂直居中

    width: 25px;
    height: 25px;
    border: 1px solid #000;
    box-sizing: border-box;
    // background: blue;
  }
  .blue{
    background: blue;
  }
  .white{
    background:#fff;
  }  
  .bomb{
    color: #000;
  }
  .flag{
    color: red;
  }
  .question{
    color:#fff;
  }
  
}

</style>
