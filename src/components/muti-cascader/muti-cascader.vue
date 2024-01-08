<template>
  <div class='cascader_wrapper'>
    <mutiCascaderContent
      class='cascader_content'
      :title='mutiTitle'
      :datas='mutiDatas'
      @update='reciveDatas'
    ></mutiCascaderContent>

    <div class='select_wrapper'>
      <div class='header'>
        <span>已选</span>
        <span class="clear" @click='handleClear()'>清空</span>
      </div>
      <ul class='content' v-if="selectDatas.length>0">
        <li class='item_wrapper' v-for='(item,index) in selectDatas' :key='`selected${index}`'>
          <span class='item_label'>
            {{ item.label }}
          </span>
          <span class='remove_btn' @click='handleRemove(item)'>
            <Icon type="md-close" />
          </span>
        </li>
      </ul>
      <div  class="select-panel-moduler-container" v-else>
        <div class="panel__selected-item">
          <div class="no-data__panel">
            <span class="empty">
              <svg  width="184" height="152" viewBox="0 0 184 152" xmlns="http://www.w3.org/2000/svg" class="icon">
              <g  fill="none" fillRule="evenodd"><g  transform="translate(24 31.67)">
                <ellipse  fillOpacity=".8" fill="#F5F5F7" cx="67.797" cy="106.89" rx="67.797" ry="12.668"></ellipse>
                <path  d="M122.034 69.674L98.109 40.229c-1.148-1.386-2.826-2.225-4.593-2.225h-51.44c-1.766 0-3.444.839-4.592 2.225L13.56 69.674v15.383h108.475V69.674z" fill="#AEB8C2"></path>
                <path  d="M101.537 86.214L80.63 61.102c-1.001-1.207-2.507-1.867-4.048-1.867H31.724c-1.54 0-3.047.66-4.048 1.867L6.769 86.214v13.792h94.768V86.214z" fill="url(#linearGradient-1)" transform="translate(13.56)"></path>
                <path  d="M33.83 0h67.933a4 4 0 0 1 4 4v93.344a4 4 0 0 1-4 4H33.83a4 4 0 0 1-4-4V4a4 4 0 0 1 4-4z" fill="#F5F5F7"></path>
                <path  d="M42.678 9.953h50.237a2 2 0 0 1 2 2V36.91a2 2 0 0 1-2 2H42.678a2 2 0 0 1-2-2V11.953a2 2 0 0 1 2-2zM42.94 49.767h49.713a2.262 2.262 0 1 1 0 4.524H42.94a2.262 2.262 0 0 1 0-4.524zM42.94 61.53h49.713a2.262 2.262 0 1 1 0 4.525H42.94a2.262 2.262 0 0 1 0-4.525zM121.813 105.032c-.775 3.071-3.497 5.36-6.735 5.36H20.515c-3.238 0-5.96-2.29-6.734-5.36a7.309 7.309 0 0 1-.222-1.79V69.675h26.318c2.907 0 5.25 2.448 5.25 5.42v.04c0 2.971 2.37 5.37 5.277 5.37h34.785c2.907 0 5.277-2.421 5.277-5.393V75.1c0-2.972 2.343-5.426 5.25-5.426h26.318v33.569c0 .617-.077 1.216-.221 1.789z" fill="#DCE0E6"></path>
              </g>
              <path  d="M149.121 33.292l-6.83 2.65a1 1 0 0 1-1.317-1.23l1.937-6.207c-2.589-2.944-4.109-6.534-4.109-10.408C138.802 8.102 148.92 0 161.402 0 173.881 0 184 8.102 184 18.097c0 9.995-10.118 18.097-22.599 18.097-4.528 0-8.744-1.066-12.28-2.902z" fill="#DCE0E6"></path>
              <g  transform="translate(149.65 15.383)" fill="#FFF"><ellipse  cx="20.654" cy="3.167" rx="2.849" ry="2.815"></ellipse><path  d="M5.698 5.63H0L2.898.704zM9.259.704h4.985V5.63H9.259z"></path></g></g></svg>
              <span  class="text">暂未选择</span>
            </span>
            </div>
          </div>
      </div>
    </div>
  </div>
</template>

<script>

import mutiCascaderContent from './muti-cascader-content'
import { Icon } from 'iview'

export default {
  name: 'muti_cascader',
  components: {
    mutiCascaderContent,
    Icon
  },
  props: {
    title: {
      type: String,
      default: '兴趣定向'
    },
    //传进来的值
    datas: {
      type: Array,
      default: () => ([])
    },
    //默认选中的值
    defaultDatas: {
      type: Array,
      default: () => ([])
    }
  },
  data () {
    return {
      //传给子组件的数据
      mutiDatas: [],
      selectDatas: []
    }
  },
  methods: {
    //提取数据传给父组件
    update () {
      let reciveDataArr = this.selectDatas.map(item => item.value)
      this.$emit('reciveDatas', reciveDataArr)
    },
    //子组件更新会传数据进来
    reciveDatas (data) {
      //最高层数据全部会传输过来
      this.selectDatas = []
      this.handleReciveCycle(data)
      this.update()
    },
    //递归，把选中的递归进去this.selectDatas
    handleReciveCycle (datas) {
      datas.map(item => {
        if (item.check) {
          this.selectDatas.push(item)
        } else {
          if (item.children && item.children.length) {
            this.handleReciveCycle(item.children)
          }
        }
        return item
      })
    },
    //检查是不是没有全部选中
    checkArrayLittle (datas) {
      for (let i = 0; i < datas.length; i++) {
        if (datas[i].check) {
          return true
        }
      }
      return false
    },
    //检查是不是全选
    checkArrayAll (datas) {
      for (let i = 0; i < datas.length; i++) {
        if (!datas[i].check) {
          return false
        }
      }
      return true
    },
    //删除元素
    handleRemove (item) {
      //过滤出选中的数据
      this.selectDatas = this.selectDatas.filter(_item => {
        return _item.value !== item.value
      })
      //把数据传输给父组件
      this.update()
      //处理数据把数据传输给子组件
      this.handleRemoveCycle(this.mutiDatas, item.value)
    },
    //清空数据，就是把所有的数据都清掉
    handleClear (){
      this.selectDatas.forEach(item=>{
        this.handleRemove(item)
      })
    },
    //datas是要传给子组件的数据，v是删除哪个元素，一层层向下处理每一个的check和little
    handleRemoveCycle (datas, v) {
      return datas.map(item => {
        //如果找到了，就会把子的改为false
        if (v === item.value) {
          item.check = false
          item.little = false
          if (item.children && item.children.length) {
            item.children = item.children.map(_item => {
              _item.check = false
              _item.little = false
              return _item
            })
          }
        } else {
          //如果没有找到，就递归去查找
          if (item.children && item.children.length) {
            item.children = this.handleRemoveCycle(item.children, v)
            //处理当前的item的check和little
            if (this.checkArrayAll(item.children)) {
              item.check = true
              item.little = false
            } else if (this.checkArrayLittle(item.children)) {
              item.check = false
              item.little = true
            } else {
              item.check = false
              item.little = false
            }
          }
        }

        //这里会返回改变后的item
        return item
      })
    },
    //设置默认的值
    setDefaultDatas (datas) {
      this.selectDatas = []
      //设置传给子组件的值
      this.mutiDatas = this.datas
      //改变mutiDatas，因为有些值是选中的
      this.handleDefaultCycle(this.mutiDatas, datas)
    },
    //设置默认的值，看里面有没有是默认为true的
    handleDefaultCycle (mutiDatas, datas) {
      return mutiDatas.map(item => {
        if (datas.includes(item.value)) {
          //如果有选中的值，就把子的都选中了
          item.check = true
          item.little = false
          if (item.children && item.children.length) {
            item.children = item.children.map(_item => {
              _item.check = true
              _item.little = false
              return _item
            })
          }
          this.selectDatas.push(item)
        } else {
          //如果不是选中的值，就改为false
          item.check = false
          if (item.children && item.children.length) {
            //递归去查询
            item.children = this.handleDefaultCycle(item.children, datas)
            if (this.checkArrayLittle(item.children)) {
              item.little = true
            }
          }
        }
        return item
      })
    }
  },
  computed: {
    mutiTitle () {
      return this.title
    }
  },
  watch: {
    defaultDatas: {
      deep: true,
      immediate: true,
      handler: function (v) {
        if (v) {
          this.setDefaultDatas(v)
        }
      }
    }
  },
  created () {
    this.mutiDatas = this.datas
  },
  mounted () {
  }
}
</script>

<style lang="less" scoped>
li {
  list-style: none;
}
.cascader_wrapper {
  overflow: hidden;
  .cascader_content {
    float: left;
    margin-right: 2rem;
  }
  .select_wrapper {
    float: left;
    border: 1px solid #dee4f5;
    font-size: 14px;
    width: 140px;
    .header {
      display: flex;
      justify-content: space-around;
      margin-bottom: 0.5rem;
      border-bottom: 1px solid #dee4f5;
      background-color: #fafbfe;
      .clear{
        cursor: pointer;
        color: #FE4552;
      }
    }
    .content {
      padding-left: 0.5rem;
      height: 200px;
      width: 100%;
      overflow-y:auto;
      .item_wrapper {
        position: relative;
        background: #edf1f5;
        margin: 4px 10px 0;
        padding: 0 6px;
        .item_label {
          margin-right: 15px;
          height: 28px;
          line-height: 28px;
        }
        .remove_btn {
          position: absolute;
          right: 8px;
          top: 7px;
          line-height: 0;
          cursor: pointer;
        }
      }
    }
  }
}


.select-panel-moduler-container{
    height: 200px;
    padding: 6px 12px;
    position: relative;
}
.panel__selected-item{
    width: 100%;
    height: 100%;
    -webkit-box-sizing: border-box;
    box-sizing: border-box;  
}
.panel__selected-item .no-data__panel{
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
    -webkit-box-align: center;
    -ms-flex-align: center;
    align-items: center;
    -webkit-box-pack: center;
    -ms-flex-pack: center;
    justify-content: center;
    height: 100%;  
}
.empty{
    display: flex;
    -webkit-box-orient: vertical;
    -webkit-box-direction: normal;
    -ms-flex-direction: column;
    flex-direction: column;
    -webkit-box-pack: center;
    -ms-flex-pack: center;
    justify-content: center;
    -webkit-box-align: center;
    -ms-flex-align: center;
    align-items: center;  
}
.empty .icon{
    width: 184px;
    height: 100px;
    margin-bottom: 12px;
}
</style>

<style>
::-webkit-scrollbar {
  -webkit-appearance: none;
  width: 7px;
}
::-webkit-scrollbar-thumb {
  border-radius: 4px;
  background-color: rgba(0, 0, 0, .5);
  -webkit-box-shadow: 0 0 1px rgba(255, 255, 255, .5);
}
</style>
