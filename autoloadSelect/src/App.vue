<template>
  <div id="app">
    <img src="./assets/logo.png">
    <p class="connet-logo">&</p>
    <img src="./assets/element.png">
    <p>select value: {{selectItem}}</p>
    <p>
      <Autoload
        :text.sync="selectItem"
        :isDisabled="isDisabled"
        :dataUrl="dataUrl"
        :dataUrlParams="dataUrlParams"
        :searchName="searchName"
        :dataOptions="dataOptions"
        :dataLabel="dataLabel"
        :dataValue="dataValue"
        @chose="choseAdd"
        :placeholder="placeholder">
      </Autoload>
    </p>
  </div>
</template>

<script>
import Autoload from './components/autoloadSelect'

export default {
  name: 'autoloadSelect',
  components: {
    Autoload
  },
  data() {
    return {
      // 数据 url,当 dataOptions 为空时必传
      dataUrl:
        'https://www.easy-mock.com/mock/591c6b989aba4141cf25b708/step/itemDataArr1',
      // 当前选中
      selectItem: '',
      // 搜索时用的字段名
      searchName: '',
      // 数据中用作 select 中 label 的字段名
      dataLabel: 'label',
      // 数据中用作 select 中 value 的字段名
      dataValue: 'value',
      // 传递固定选项，不使用滚动加载功能
      dataOptions: [],
      // 是否禁用
      isDisabled: false,
      // 数据 url 固定使用的 query
      dataUrlParams: '',
      // placeholder
      placeholder: '请选择'
    }
  },
  methods: {
    choseAdd() {}
  }
}
</script>

<style lang="less">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  .connet-logo{
    font-size: 25px;
    font-weight: bold;
  }
}
</style>
