<template>
  <el-select
  v-model="value" 
  class="autoload" 
  ref="elselect"
  filterable
  remote
  :remote-method="inputDect"
  @visible-change="visibleChange" 
  :placeholder="placeholder" 
  size="mini" 
  @change="changeChose" >
    <el-option  
      ref="eloptionli"
      v-for="item in optionsD"
      :key="item.value"
      :label="item.label"
      :disabled="item.disabled||false"
      :value="item.value">
    </el-option>
  </el-select>
</template>
<script>
export default {
  name: 'autoloadSelect',
  props: {
    options: {
      type: Array
    },
    text: {},
    placeholder: {
      type: String,
      default: '全部'
    }
  },
  data () {
    return {
      loadFlag: true,
      value: this.text,
      optionsD: [],
      drop: '',
      dropUl: ''
    }
  },
  mounted () {
    // 可搜索的 select，加个箭头
    this.$refs.elselect.$children[0].$el.childNodes[3].childNodes[0].children[0].className = 'el-select__caret el-input__icon el-icon-arrow-up'
  },
  watch: {
    options (val, oldVal) {
       // 新数据长度为0时，清空选项
      if (val.length === 0) {
        this.optionsD = []
        this.optionsD.push({
          label: '无数据',
          value: 'load-item',
          disabled: true
        })
        return
      }
      // 判断新旧数据是否相同，新数据长度比旧数据大时，替换为新数据
      // 新数据长度大于10，旧数据长度为0时，添加不可点击的“已加载全部”标签
      let _oldVal = JSON.stringify(oldVal),
        _val = JSON.stringify(val)
      if (val.length > oldVal.length && _oldVal !== _val) {
        let _options = JSON.parse(_val)
        this.$nextTick(() => {
          this.optionsD = _options
          this.visibleChange()
          if (val.length < 10 && oldVal.length === 0) {
            this.optionsD.push({
              label: '已加载全部',
              value: 'load-item',
              disabled: true
            })
          }
          this.loadFlag = true
        })
      } else if (this.optionsD.length > 0 && !this.loadFlag) {
        this.optionsD[this.optionsD.length - 1].label = '已加载全部'
        this.dropUl = this.$refs.eloptionli[0].$el.parentElement
        this.drop = this.dropUl.parentElement
        this.drop.scrollTop = this.dropUl.clientHeight - this.drop.clientHeight
      }
    },
    value (val) {
      this.$emit('update:text', val)
    },
    text (val) {
      this.value = this.text
    }
  },
  methods: {
    visibleChange () {
      if (this.options.length === 0) {
        return
      }
      if (this.$refs.eloptionli && this.$refs.eloptionli[0]) {
        this.drop = this.$refs.eloptionli[0].$el.parentElement.parentElement
        this.dropUl = this.$refs.eloptionli[0].$el.parentElement
        this.drop.addEventListener('scroll', this.load)
      }
    },
    inputDect (val) {
      this.$emit('input', val)
    },
    load () {
      if (
        this.dropUl.clientHeight -
        (this.drop.scrollTop + this.drop.clientHeight) <
        30 &&
        this.loadFlag
      ) {
        let loadItem = {}
        loadItem.label = '加载中...'
        loadItem.value = 'load-item'
        loadItem.disabled = true
        this.optionsD.push(loadItem)
        this.$emit('load')
        this.loadFlag = false
      }
    },
    changeChose (val) {
      this.$emit('chose', val)
    }
  }
}
</script>
<style lang="less">
.autoload {
  margin: 0;
  input-placeholder {
    color: #333 !important;
  }
  &:hover {
    input-placeholder {
      color: #ff0036 !important;
    }
    .el-icon-arrow-up {
      color: #ff0036 !important;
    }
  }
}
</style>
