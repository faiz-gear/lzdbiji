---
title: echarts学习笔记
tag: echarts
keywords: echarts
categories: 前端可视化库
---
### 一、柱状图（type为bar）

#### 1.数据格式

一维数组

#### 2.xAxis和yAxis

type为category时，需要指定data

type为value时，data在series中指定

#### 3.常见效果

- 标记：最大值、最小值、平均值

  ```javascript
  markPoint：{
    data: [
      {
        type: 'max'，
        name: '最大值'
      },
      {
        type: 'min',
        name: '最小值'
      }
    ]
  },
    markLine: {
      data: [
        {
          type: 'average'，
          name: '平均值'
        }
      ]
    }
  ```


- 显示：

  label：数值显示

  barWidth： 柱宽度

  横向柱状图：xAxis和yAxis配置互换

### 二、通用配置

- title标题
  - 文字样式：textStyle
  - 标题边框：borderWidth、borderColor、borderRadius
  - 标题位置：left、top、right、bottom
- tooltip提示框组件
  - 触发类型：trigger
    - item
    - axis
  - 触发时机：triggerOn
    - mouseover
    - click
  - 格式化：formatter
    - 字符串模板
    - 回调函数（参数：arg）
- toolbox工具栏（导出图片、数据视图、数据区域缩放、重置、动态类型切换五个工具）
  - feature（显示工具栏按钮）
    - savaAsImage（导出图片）
    - dataView（数据视图）
    - dataZoom（数据区域缩放）
    - restore（重置）
    - magicType（动态类型切换）：如magicType： { type: ['bar', 'line']}
- legend图例（用于筛选系列，需要和series配合使用）
  - legend中的data是一个数组
  - legend中的data的值需要和series数组中某组数据的name值一致

### 三、折线图（type为line）

#### 1.数据格式

一维数组

#### 2.常用效果

- 标记：最大值、最小值、平均值、标注区间
  - markPoint
  - markLine
  - markArea
- 线条控制：平滑、风格
  - smooth、lineStyle 
- 填充风格
  - areaStyle
- 紧挨y轴边缘（设置在xAxis）
  - boundaryGap
- 缩放：脱离0值比例
  - scale：true
- 堆叠图
  - stack：all
  - 可以和areaStyle配合使用

### 四、散点图（type为scatter）

#### 1.数据格式

二维数组

#### 2.基本的散点图

- x轴和y轴type都是value
- x轴和y轴的数据，是一个二维数组
- type为scatter

#### 3.常用效果

- 气泡图效果
  - 散点大小不同：symbolSize
  - 散点的颜色不同：itemStyle.color
- 涟漪动画效果
  - type: effectScatter
  - showEffectOn: 'emphasis' 鼠标移入时才显示涟漪效果，默认值是render（渲染就显示涟漪效果）
  - rippleEffect: { scale: 10} 散点涟漪效果的范围

### 五、直角坐标系的常用配置

直角坐标系的图表：柱状图（bar）、折线图（line）、散点图（scatter）

- grid网格（控制直角坐标系的布局和大小，x轴和y轴就是在grid的基础上进行绘制的）
  - 显示gird
    - show
  - grid的边框
    - borderWidth、borderColor
  - grid的位置和大小
    - left
    - top
    - right
    - bottom
- axis坐标轴（分为x轴和y轴）
  - 坐标轴类型type
    - value：数值周，自动会从目标数据中读取数据
    - category：类目轴，该类型必须通过data设置类目数据
  - 显示位置position
    - xAxis：可取值为top或bottom
    - yAxis：可取值为left或right
- 区域缩放dataZoom
  - dataZoom用于区域缩放，对数据范围过滤，x轴和y轴都可以拥有。dataZoom是一个数组，可以配置多个区域缩放器
  - 类型type
    - slider：滑块控制缩放
    - inside：内置，依靠鼠标滚轮或者双指缩放
  - 指明产生作用的轴
    - xAxisIndex：设置缩放组件控制的是哪个x轴，一般写0即可
    - yAxisIndex：设置缩放组件控制的是哪个y轴，一般写0即可
  - 指明初始状态的缩放情况
    - start：数据窗口范围的起始百分比
    - end：数据窗口范围的结束百分比

### 六、饼图（type为pie）

#### 1.数据格式

json数据：data: [{name: '京东'，value: '9000'}]

#### 2.基本的饼图

- 数据是json数据格式
- type为pie
- 无须配置xAxis和yAxis

#### 3.饼图常见效果

- 显示数值
  - lable.formatter
- 圆环
  - 设置两个半径 radius：['50%', '70%'] 分别是内半径和外半径
- 南丁格尔图
  - roseType：'radius'
- 选中效果
  - 选中模式 selectMode：single（可选中单个）\multiple（可选中多个）
  - 选中偏移量 selectOffset：30

### 七、地图（type：map）

#### 1.矢量地图的实现步骤

- Echarts最基本的代码结构
  - 引入js文件
  - 带宽高的DOM容器
  - init初始化对象
  - setOption设置option
- 准备中国的矢量地图json文件（http://datav.aliyun.com/tools/atlas/index.html#&lat=30.332329214580188&lng=106.72278672066881&zoom=3.5）
- 使用ajax获取json
  - $.get('json/map/china.json', function(chinaJson){})
- 往echarts全局对象注册地图的json数据
  - echarts.registerMap('chinaMap', chinaJson)
- 在geo下设置
  - type:'map'
  - map:'chinaMap'

#### 2.常用配置

- 缩放拖动
  - roam：true
- 名称显示
  - label
- 初始缩放比例
  - zoom：2（2倍）
- 地图中心点
  - center

#### 3.常见效果

- 不同城市颜色不同（如空气质量）

  1. 显示基本的中国地图

  2. 城市的空气质量数据设置给series（此处的数据必须是[{name: cityName, value: ''}]的格式，其中name属性必须和省份名一致）

  3. 将series下的数据与geo关联起来

     设置type: 'map'，geoIndex:0

  4. 结合visualMap配合使用

     visualMap: {min: 0, max: 40, range: { color: ['white', 'red']}}

- 地图和散点图结合

  1. 给series下配置散点图对象

  2. 准备好散点数据，设置给新对象的data（二维数组，地图坐标）

  3. 配置新对象的type为effectScatter

  4. 让散点图使用地图坐标系统

     coordinateSystem: 'geo'

  5. 让涟漪的效果更加明显

     rippleEffect：{ scale：10 }

### 八、雷达图（type：radar）

#### 1.实现步骤

- echarts最基本的代码结构
- 定义各个维度的最大值（在radar属性下）
  - indicator：[{name: '易用性', max: 100]}]
- 准备具体产品的数据
  - 在series下设置data：[{name: '华为手机', value: [80, 80, 90, 95, 85]}
- 设置图表类型
  - 在series下设置type：radar

#### 2.常用配置

- 显示数值:
  - label
- 区域面积（阴影）
  - areaStyle
- 绘制类型
  - shape: 'circle' 默认值 polygon

### 九、仪表盘（type：guage）

#### 1.实现步骤

- echarts最基本的代码结构
- 准备数据，设置给series下的data
  - data: [{value: 97}]
- 图标类型
  - 在series下设置type：guage

#### 2.常用效果

- 数值范围
  - max
  - min
- 多个指针
  - 增加series下data数组中的元素
- 多个指针颜色差异
  - itemStyle

### 十、主题

#### 1.内置主题

- echarts中默认内置了两套主题：light、dark

- 在初始化对象方法中init中可以指明

  var chart = echarts.init(dom, 'light')

  var chart = echarts.init(dom, 'dark')

#### 2.自定义主题

- 1.在官网主题编辑器中编辑主题
- 2.下载主题js文件
- 3.引入主题js文件
- 4.在init方法中使用主题

### 十一、调色盘

#### 1.它是一组颜色，图形、系列会自动从其中选择颜色

- 主题调色盘（主题js文件中全局注册的color）

- 全局调色盘

  options：{

  ​	color： ['red', 'green', 'blue']

  }

- 局部调色盘

  series: [{

  ​	type: 'bar',

  ​	color: ['red', 'green', 'blue']

  }]

#### 2.调色盘的作用遵循就近原则

#### 3.颜色渐变

- 线性渐变

  ```javascript
  itemStyle: {
    color: {
      type: 'linear',
        x: 0, // 在图元中的初始x位置
        y: 0, // 在图元中的初始y位置
        x2: 0,
        y2: 1, // 1代表到底部，0.5在中间
          colorStops: [{
            offset: 0, color: 'red' // 0%处的颜色 
          },{
            offset: 1, color: 'blue' // 100%处的颜色
          }]
    }
  }
  ```

- 径向渐变

  ```javascript
  itemStyle: {
    color: {
      type: 'radial',
        x: 0.5, 
        y: 0.5,
        r: 0.5, // 扩散的半径
          colorStops: [{
            offset: 0, color: 'red'
          }, {
            offset: 1, color: 'blue'
          }]
    }
  }
  ```

### 十二、样式

- 直接样式
  - itemStyle、textStyle、lineStyle、areaStyle、label
- 高亮样式
  - 在emphasis中包裹itemStyle、textStyle、lineStyle、areaStyle、label
- 优先级高，会覆盖主题中、调色盘的效果

### 十三、自适应

当浏览器的大小发生变化的时候，如果想让图表也能随之适配变化

- 1.监听窗口大小变化事件

- 2.在事件处理函数中调用echarts实例对象的resize方法即可

  window.onresize = myChart.resize

### 十四、加载动画

echarts已经内置好了加载数据的动画，我们只需要在合适的时机显示或者隐藏即可

- 显示加载动画

  mCharts.showLoading()

- 隐藏加载动画

  mCharts.hideLoading()

### 十五、增量动画

- 增量动画的实现方式
  - mCharts.setOption
    - 所有数据的更新都通过setOption实现
    - 不用考虑数据到底产生了哪些变化
    - echarts会找到两组数据之间的差异然后通过合适的动画取实现数据的变化
    - 新旧option的关系并不是相互覆盖的关系，而是相互整合的关系
    - 我们在设置新的option的时候，只需要考虑到变化的部分就可以

### 十六、动画配置项

- 开启动画

  - animation：true

- 动画时长

  - animationDuration：5000

  - ```javascript
    animationDuration： function（arg） {
      // 这里的arg是所有需要动画效果的图元的索引，会根据不同形式的图元元素进行分组
      return arg * 10
    }
    ```

- 缓动动画

  - animationEasing: 'bounceOut'

- 动画阈值

  - animationThreshold： 8
  - 单中形式的元素数量大于这个阈值会关闭动画

### 十七、全局Echarts对象常用方法

- init方法

  - 初始化Echarts实例对象
  - 使用主题（第二个参数）

- registerTheme方法

  - 注册主题
  - 只有注册过的主题，才能在init方法中使用该主题

- registerMap方法

  - 注册地图数据

    - ```javascript
      $.get('json/map/china.json', function(ChinaJson) {
        echarts.registerMap('china', chinaJson)
      })
      ```

  - geo组件使用地图数据

    - ```javascript
      var option = {
        geo: {
          type: 'map',
          map: 'china'
        }
      }
      ```

- connect方法

  - 一个页面中可以有多个独立的图表
  - 每一个图表对应一个Echarts实例对象
  - connect可以实现多图关联，传入联动目标为Echarts实例对象，支持数组
    - 保存图片的自动拼接（saveAsImage）
    - 刷新按钮
    - 重置按钮
    - 提示框联动、图例选择、数据范围修改等等

### 十八、echartsInstance实例常用方法

- setOption方法

  - 设置或修改图表实例的配置项以及数据
  - 多次调用setOption方法
    - 合并新旧配置
    - 增量动画

- resize方法

  - 重新计算和绘制图表
  - 一般和window对象的resize事件结合使用
    - window.onresize = mEcharts.resize

- on\off方法

  - 绑定或者解绑事件处理函数
  - 鼠标事件
    - 常见事件：click、dbclick、mousedown、mousemove、mouseup等
  - Echarts事件
    - 常见事件：legendselectchanged、datazoom、pieselectchanged、ma'pselectchanged

- dispatchAction

  - 触发某些行为

  - 使用代码模拟用户的行为

  - ```javascript
    mCharts.dispatchAction({
      type: 'hightlight', // 事件类型
      seriesIndex: 0, // 图表索引(系列)
      dataIndex: 1 // 图表中哪一项高亮
    })
    ```

- clear

  - 清空当前实例，会移除实例中所有的组件和图表
  - 清空之后可以再次setOption

- dispose方法

  - 销毁实例
  - 销毁后实例无法再被使用

### 十九、websocket的基本使用

- 后端

  - 安装包

    - npm i ws

  - 创建对象

    - ```javascript
      const webSocket = require('ws')
      const wss = new WebSocket.server({
        port: '8080'
      })
      ```

  - 监听事件

    - 连接事件

      - ```javascript
        wss.on('connection', client => {
          console.log('有客户端连接')
        })
        ```

    - 接受数据事件

      - ```javascript
        wss.on('connection', client => {
          console.log('有客户端连接')
          client.on('message', msg => {
            console.log('客户端发送数据过来了')
          })
        })
        ```

    - 发送数据

      - client.send('hello socket from back-end')

- 前端

  - 创建对象

    - ```javascript
      const ws = new WebSocket('ws://localhost:8080')
      ```

  - 监听事件

    - 连接成功事件
      - ws.onopen = () => {}
    - 接受数据事件
      - ws.onmessage = msg => {}
    - 关闭连接事件
      - ws.onclose = () => {}

  - 发送数据

    - ws.send