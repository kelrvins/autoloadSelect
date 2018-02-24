<template>
  <div id="app">
    <img src="./assets/logo.png">
    <p>search world: {{searchWorld}}</p>
    <p>select value: {{selectItem}}</p>
    <p>
      <Autoload
        :text.sync="selectItem"
        :options="selectItemArr"
        placeholder="请选择"
        @load="getSelectItemArr"
        @clear="clearSelect"
        @input="selectInput"
      ></Autoload>
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
  data () {
    return {
      selectItem: '',
      selectItemArr: [],
      searchWorld: ' '
    }
  },
  mounted () {
    this.getSelectItemArr()
  },
  methods: {
    getSelectItemArr () {
      this.$http
        .get('https://www.easy-mock.com/mock/591c6b989aba4141cf25b708/step/itemDataArr1')
        .then(response => {
          if (response.status === 200) {
            let item = response || []
            console.log(item.data.data.sea)
            this.selectItemArr = [...this.selectItemArr, ...item.data.data.sea]
          }
        })
        .catch(error => {
          console.log(error)
        })
    },
    selectInput (val) {
      this.searchWorld = val
      if (val.trim() === '') {
        this.clearSelect()
        return
      }
      this.$http
        .get('https://www.easy-mock.com/mock/591c6b989aba4141cf25b708/step/itemDataArrSearch?sea=' + val)
        .then(response => {
          if (response.status === 200) {
            let item = response || []
            this.selectItemArr = item.data.data.sea
          }
        })
        .catch(error => {
          console.log(error)
        })
    },
    clearSelect () {
      this.selectItem = ''
      this.selectItemArr = []
      this.searchWorld = ''
      this.getSelectItemArr()
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
