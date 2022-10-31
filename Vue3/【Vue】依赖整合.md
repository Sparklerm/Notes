# 引入Bootstrap

## **安装依赖包**

```shell
npm install bootstrap --save-dev
npm install jquery --save-dev
npm install popper.js --save-dev
```

## **全局引入**

>   在项目中根目录下的main.js中添加如下代码：

```js
import 'bootstrap'
import $ from 'jquery'
```

## **在vue文件中引用**

```html
<script>
import 'bootstrap/dist/css/bootstrap.min.css'
import 'bootstrap/dist/js/bootstrap.min.js'
</script>
```



# 引入ECharts

## 安装ECharts

```shell
npm install echarts --save
```

## 全局引入ECharts main.js

```js
// 引入echarts
import echarts from 'echarts'
 
Vue.prototype.$echarts = echarts
```