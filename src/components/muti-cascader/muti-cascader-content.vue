<template>
  <div class='wrapper_box'>
    <div class='wrapper'>
      <div class='header'>
        {{ title }}
      </div>
      <ul class='content'>
        <li class='title'>
          <Checkbox :value='isCheckAll' @on-change='handleCheckAll'>全选</Checkbox>
        </li>
        <li class='item_wrapper' v-for='(item,index) in entites' :key='item.id'>
          <span class='check_box'>
            <Checkbox v-model='item.check'
                      @on-change='handleSelect($event,item,index)'
                      :indeterminate='item.little'></Checkbox>
            <span class='check_label' @click='handleChildrenDatas(item,index,item.check)'>{{ item.label }}</span>
          </span>
          <span class='icon' @click='handleChildrenDatas(item,index,item.check)'>
            <Icon v-show='item.children&&item.children.length>0' type="ios-arrow-forward" />
          </span>
        </li>
      </ul>
    </div>
    <multiCascader
      v-if='childrenDatas.length'
      :title='childrenTitle'
      :datas='childrenDatas'
      @update='childUpdate'></multiCascader>
  </div>
</template>

<script>
import { Checkbox, Icon } from 'iview'

export default {
  name: 'multiCascader',
  components: {
    Checkbox,
    Icon
  },
  props: {
    title: {
      type: String,
      default: '兴趣定向'
    },
    datas: {
      type: Array,
      default: () => ([])
    }
  },
  data () {
    return {
      entites: [],
      childrenDatas: [],
      selectedDatas: [],
      selectedIndex: -1,
      childrenCheck: false,
    }
  },
  computed: {
    //是否全选中
    isCheckAll () {
      return this.checkArrayAll(this.entites)
    }
  },
  methods: {
    //检查里面是否有flase的，如果没有就是全选，如果有就不是全选
    checkArrayAll (datas) {
      for (let i = 0; i < datas.length; i++) {
        if (!datas[i].check) {
          return false
        }
      }
      return true
    },
    //检查是否选择横杆
    checkArrayLittle (datas) {
      for (let i = 0; i < datas.length; i++) {
        if (datas[i].check || datas[i].little) {
          return true
        }
      }
      return false
    },
    //全选按钮
    handleCheckAll (checked) {
      this.entites = this.entites.map(item => {
        item.check = checked
        item.little = false
        //递归修改
        this.handleCheckAllChildren(item,checked)
        return item
      })
      //需要更新父组件
      this.updateToParent()
    },
    //递归修改
    handleCheckAllChildren(parentItem,checked){
      if (parentItem.children && parentItem.children.length) {
        parentItem.children = parentItem.children.map(childItem => {
          childItem.check = checked;
          childItem.little = false;
          this.handleCheckAllChildren(childItem, checked);
          return childItem;
        });
      }
    },
    //点击展示右边
    handleChildrenDatas (item, index, checked) {
      this.selectedIndex = index
      if (item.children && item.children.length) {
        this.childrenDatas = item.children
        this.childrenTitle = item.label
      }
    },
    //选中那个数据，现在会递归到最底部，而且会把横杆改为false
    handleSelect (checked, item, index) {
      //如果是选中变成未选中，就要把横线改为false
      if (checked)item.little = false
      //赋值当前选中的selectedIndex
      this.selectedIndex = index
      if (checked.children && checked.children.length) {
        // 设置后续展示需要用到的值
          this.childrenDatas = parentItem.children;
          this.childrenTitle = parentItem.label;
      }      

      // 递归处理子节点,2024/1/8
      this.handleChildren(item, checked);    
      this.updateToParent()
    },
    handleChildren(parentItem, checked) {
      if (parentItem.children && parentItem.children.length) {
        parentItem.children = parentItem.children.map(childItem => {
          childItem.check = checked;
          childItem.little = false;
          this.handleChildren(childItem, checked);
          return childItem;
        });
      }
  },    
    //更新这个
    childUpdate (childDatas) {
      
      //把它更新到当前子
      this.entites[this.selectedIndex].children = Object.assign([], childDatas)
      //判断是否全选
      this.entites[this.selectedIndex].check = this.checkArrayAll(childDatas)

      //判断是否选择横杆
      if (this.entites[this.selectedIndex].check) {
        this.entites[this.selectedIndex].little = false
      } else {
        this.entites[this.selectedIndex].little = this.checkArrayLittle(childDatas)
      }
      this.updateToParent()
    },
    //传给父组件，一级一级传，直到顶部
    updateToParent () {
      this.$emit('update', this.entites)
    }
  },
  watch: {
    datas: {
      deep: true,
      immediate: true,
      handler: function (v) {
        if (v && v.length) {
          this.entites = Object.assign([], v)
        }
      }
    }
  },
  created () {
  },
  mounted () {
  }
}
</script>

<style lang="less" scoped>
li {
  list-style: none;
}
.wrapper_box {
  overflow: hidden;
  .wrapper {
    float: left;
    border: 1px solid #dee4f5;
    font-size: 14px;
    width: 140px;
    .header {
      text-align: center;
      margin-bottom: 0.5rem;
      border-bottom: 1px solid #dee4f5;
      background-color: #fafbfe;
    }
    .content {
      padding-left: 0.5rem;
      width: 100%;
      height: 200px;
      overflow-y:auto;
      .item_wrapper {
        display: flex;
        align-items: center;
        justify-content: space-between;
        .check_box {
          .check_label {
            display: inblock;
            width: 80;
            cursor: pointer;
          }
        }
        // .icon {
        //   padding-right: 0.5rem;
        // }
      }
    }
  }
}
</style>
