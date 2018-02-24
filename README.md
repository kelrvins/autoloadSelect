# autoloadSelect
element的可搜索 select，添加自动加载

## usage
> html
```
<Autoload 
  :text.sync="selectItem" 
  :options="selectItemArr" 
  placeholder="请选择"
  @load="getSelectItemArr" 
></Autoload>
```
> javascript
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
    getSelectItemArr () {
      /*
        code
      */
    }
  }
}
```