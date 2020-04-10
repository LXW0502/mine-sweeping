<template>
  <div class="control">
    <div class="item">时间：<el-input v-model="time" disabled></el-input></div>
    <div class="item">雷量：<el-input v-model="count" disabled></el-input></div>
    <el-button type="primary" @click="isShowDialog = true;">新建游戏</el-button>

    <el-dialog
      title="新建游戏"
      :visible.sync="isShowDialog"
      :close-on-click-modal="false">
      <el-form :model="formData">
        <el-form-item label="宽度"><!-- .number将字符串类型的数据转为数字类型 -->
          <el-input v-model.number="formData.width"></el-input>
        </el-form-item>
        <el-form-item label="高度">
          <el-input v-model.number="formData.height"></el-input>
        </el-form-item>
        <el-form-item label="雷数量">
          <el-input v-model.number="formData.bombCount"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="isShowDialog = false">取 消</el-button>
        <el-button type="primary" @click="submit()">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>

export default {
  data(){//储存数据
    //control的数据收集在formData，点击submit时，通过emit使数据子传父
    return {
      isShowDialog: false,
      time: 0,
      count: 0,

      formData: {//对象是引用型数据类型typeof，this.formData、gameData、gameDataFromIndex指向同一个内存地址，所以改变宽度高度数据，表格数目立即改变
        width: 40,
        height: 30,
        bombCount:10,
      },
    };
  },
  methods: {//储存方法
    submit(){
      //对象是引用型数据类型,五种基础数据类型不是引用型，想formData中的数据和main中不是同一个，则可以对对象this.formData进行一个拷贝,this.formData可是对象或者数组。先将对象转为转为字符串，再将字符串转为一个对象
      var data = JSON.parse(JSON.stringify(this.formData));//JSON.stringify将对象转为字符串，JSON.parse将字符串转为对象，此时data是一个新的对象
      console.log(data == this.formData);//false
      this.$emit('startGame',data);//通过$emit实现子（子组件）传父（父组件index.vu引用了game-control），startGame为自定义事件名，将this.formData数据得到，在父组件搜集到触发的事件，见index页面
      this.isShowDialog = false;
    },
  },
  components: {//注册组建

  },
}
</script>

<style lang="scss" scoped>
.control{
  display: flex;
  justify-content: center;
  .item{
    display: flex;
    align-items: center;
    white-space: nowrap;
    margin-right: 10px;
  }
}
/deep/{
  .el-input.is-disabled .el-input__inner{
    width: 100px;
  }
}
</style>
