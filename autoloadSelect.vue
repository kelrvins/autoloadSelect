<template>
  <div class="el-select">
    <el-select
    v-model="value"
    class="autoload"
    ref="elselect"
    clearable
    filterable
    :filter-method="inputDect"
    @visible-change="visibleChange"
    :disabled="isDisabled"
    :placeholder="placeholder"
    size="mini"
    @clear="clearSelect"
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
  </div>
</template>
<script>
export default {
  name: 'autoloadSelect',
  props: {
    dataUrl: '',
    dataUrlParams: '',
    dataLabel: '',
    searchId: '',
    searchName: '',
    dataOptions: {
      type: Array,
      default() {
        return []
      }
    },
    dataValue: '',
    text: {},
    placeholder: {
      type: String,
      default: '全部'
    },
    isDisabled: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      loadFlag: true,
      value: this.text,
      optionsD: [],
      drop: '',
      dropUl: '',
      start: 0,
      limit: 10,
      selectLength: 0,
      searchFlag: ''
    }
  },
  mounted() {
    this.getOptionName()
    this.loadSelect()
  },
  watch: {
    dataOptions: {
      handler(val, oldVal) {
        if (val.length !== 0) {
          this.optionsD = val
        }
      },
      deep: true
    },
    dataUrl(val, oldVal) {
      this.start = 0
      this.optionsD = []
      this.loadSelect()
    },
    value(val) {
      this.$emit('update:text', val)
    },
    text(val) {
      this.value = this.text
    }
  },
  methods: {
    visibleChange() {
      if (this.$refs.eloptionli && this.$refs.eloptionli[0]) {
        this.drop = this.$refs.eloptionli[0].$el.parentElement.parentElement
        this.dropUl = this.$refs.eloptionli[0].$el.parentElement
        this.drop.addEventListener('scroll', this.load)
      }
    },
    inputDect(val) {
      if (this.dataOptions.length > 0) {
        return
      }
      const _this = this
      clearTimeout(_this.searchFlag)
      _this.searchFlag = setTimeout(function() {
        _this.loadFlag = false
        if (val === '') {
          _this.start = 0
          _this.optionsD = []
          _this.loadSelect()
        } else {
          _this.inputSelect(val)
        }
      }, 1000)
    },
    inputSelect(val) {
      let $url = this.dataUrl + `?${this.searchName}=${val}`
      if (this.dataUrlParams) {
        $url = this.dataUrl + `?${this.dataUrlParams}&${this.searchName}=${val}`
      }
      this.$http
        .get($url)
        .then(res => {
          this.loadFlag = true
          if (res.status === 200) {
            let data = res.data.itemList
            this.optionsD = []
            if (data.length > 0) {
              data.forEach(el => {
                this.optionsD.push({
                  label: el[this.dataLabel],
                  value: el[this.dataValue]
                })
              })
            }
          }
        })
        .catch(error => {
          this.optionsD = []
          this.loadFlag = true
          this.$refs.errcatch.init(error)
        })
    },
    load() {
      if (
        this.dropUl.clientHeight -
          (this.drop.scrollTop + this.drop.clientHeight) <
          30 &&
        this.loadFlag &&
        this.selectLength > this.start
      ) {
        let loadItem = {}
        loadItem.label = '加载中...'
        loadItem.value = 'load-item'
        loadItem.disabled = true
        this.optionsD.push(loadItem)
        this.loadFlag = false
        this.loadSelect()
      }
    },
    clearSelect() {
      this.start = 0
      this.optionsD = []
      this.loadSelect()
    },
    changeChose(value) {
      let label = ''
      let labelObject = this.optionsD.filter(el => {
        return el.value === value
      })
      if (labelObject.length > 0) {
        label = labelObject[0].label
      }
      this.$emit('chose', value, label)
    },
    getOptionName() {
      if (!this.text || !this.searchId) return
      this.$http
        .get(`${this.dataUrl}?${this.searchId}=${this.text}`)
        .then(res => {
          console.log(res)
          let data = res.data.itemList
          if (data.length > 0) {
            data.forEach(el => {
              this.optionsD.push({
                label: el[this.dataLabel],
                value: el[this.dataValue]
              })
            })
          }
        })
        .catch(error => {
          console.log(error)
        })
    },
    loadSelect() {
      if (this.dataOptions.length > 0 || !this.dataUrl) {
        return
      }
      let $url = `${this.dataUrl}?limit=${this.limit}&start=${this.start}`
      if (this.dataUrlParams) {
        $url = `${this.dataUrl}?${this.dataUrlParams}&limit=${
          this.limit
        }&start=${this.start}`
      }
      this.$http
        .get($url)
        .then(res => {
          this.loadFlag = true
          if (res.status === 200) {
            this.selectLength = res.data.totalCount
            let data = res.data.itemList
            if (data.length > 0) {
              this.optionsD.pop()
              data.forEach(el => {
                this.optionsD.push({
                  label: el[this.dataLabel],
                  value: el[this.dataValue]
                })
              })
              this.start += 10
              if (this.selectLength <= this.start) {
                this.optionsD.push({
                  label: '已加载全部',
                  value: 'load-item',
                  disabled: true
                })
              }
            }
          }
        })
        .catch(error => {
          console.log(error)
          this.loadFlag = true
        })
    }
  }
}
</script>
<style lang="less" scoped>
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
