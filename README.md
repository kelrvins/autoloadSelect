# autoloadSelect
element的可搜索 select，添加自动加载

<p align="center"><img width=100% src="./autoload.gif"></p>
## usage
> HTML
```
<Autoload 
  :text.sync="selectItem" 
  :options="selectItemArr" 
  placeholder="请选择"
  @load="getSelectItemArr"
  @input="selectInput"
></Autoload>
```
> Javascript
```
import Autoload from './autoloadSelect'
export default {
  // 注册组件
  components: {
    Autoload
  },
  data () {
    return {
      selectItem: 0,
      selectItemArr: []
    }
  },
  methods: {
    // 获取可选项
    getSelectItemArr () {
      /*
        code
      */
    },
    // 搜索选项
    selectInput (val) {
      /*
        code
      */
    }
  }
}
```