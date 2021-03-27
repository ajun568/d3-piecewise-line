# “D3.js” 手绘分段折线图

> Hello, 各位勇敢的小伙伴, 大家好, 我是你们的嘴强王者小五, 身体健康, 脑子没病.
>
> 本人有丰富的脱发技巧, 能让你一跃成为资深大咖.
>
> 一看就会一写就废是本人的主旨, 菜到抠脚是本人的特点, 卑微中透着一丝丝刚强, 傻人有傻福是对我最大的安慰.
>
> 欢迎来到`小五`的`随笔系列`之`“D3.js” 手绘分段折线图`.

## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn dev
```

### Compiles and minifies for production
```
yarn build
```

### Lints and fixes files
```
yarn lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

## 写在前面

**双手奉上代码链接** [传送门 - ajun568](https://github.com/ajun568/d3-piecewise-line)

**双脚奉上最终效果图**

![d3line17.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/429d6c949e0447888522d246713e0f68~tplv-k3u1fbpfcp-watermark.image)

**观前提醒**

👺 本文以实现上图为最终目的，所有过程均服务于结果，而非对`svg`、`D3.js`的系统学习。

**导读**

👺 朋友，你是否与我有相同的功能诉求，使用主流的图表库不易满足我们的需求；朋友，你是否又和我一样急于求成，想在短时间内完成相应的功能开发。如果屏幕前你也有相同的想法, 那*D3*是一个不错的选择，它易于上手且可针对需求定制化绘制。下面就让我们一同进入这个充满奇幻色彩的图形世界吧❗️

## 准备工作

**yarn add d3**

<font size=1 color=DarkSalmon>截止`2021-03`，当前最新版本`d3^6.6.0`，我们以此版本来展开旅程。万变不离其宗，如若版本更替，建议采用最新版本。</font>

## 🗡解剖

**拟定数据格式如下**

```js
dataset = [
  {
    xValue: x轴数据 | Number,
    yValue: y轴数据 | Number,
    filled: 是否为实心点 | Boolean,
  },
  ...[and so on]
]
```

**look 👆 picture**

可将其拆解为以下几个部分:

* 坐标轴 $(x, y)$

* 坐标点 & 点到坐标轴的虚线

* 路径

* tooltip & hover时点到坐标轴的虚线

## 浅谈SVG

普通场景下 $d3.js$ 就是 $svg$ 的语法糖

既然通篇都要与 $svg$ 打交道，怎么能不认识一下这个可爱的小家伙呢，所谓知己知彼，百战不殆

**viewport => width / height**: 指定画布的宽度和高度

```html
<svg width="800" height="400"></svg>
```

**viewBox => (x, y, width, height)**: 从$(x, y)$点, 向正方向选取宽为$width$, 高为$height$的矩形, 并放大至画布大小

正方向如图:

![line1.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7b12cfd63e734d2185e6e1536fb59e71~tplv-k3u1fbpfcp-watermark.image)

**来几段代码感受一下**

**🧟‍♂️ Code**

```html
<svg width="400" height="300" viewBox="0, 0, 400, 300">
  <rect width="20" height="15" fill="red"/>
</svg>
```

**🧟‍♂️ Image**

![line2.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/595069d046f2448f86210c4cd3a01db6~tplv-k3u1fbpfcp-watermark.image)

**🧟‍♂️ Code**

```html
<svg width="400" height="300" viewBox="10, -150, 400, 300">
  <rect width="20" height="15" fill="red"/>
</svg>
```

**🧟‍♂️ Image**

![line3.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aa879404062e41b7adff933ced923432~tplv-k3u1fbpfcp-watermark.image)

此图为在(10, -150)位置, 向正方向选取400✖️300的画布并展示

**🧟‍♂️ Code**

```html
<svg width="400" height="300" viewBox="-10, -10, 40, 30">
  <rect width="20" height="15" fill="red"/>
</svg>
```

**🧟‍♂️ Image**

![line4.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1a1332975313401c80099c30c2ce3f77~tplv-k3u1fbpfcp-watermark.image)

此图相当于将选取的元素放大了20倍

**常见标签**

**rect** 绘制矩形 `@params => x, y, width, height`，$x, y$ 为矩形偏移量。上文都是以矩形举例的，就不在赘述了。

**circle** 绘制圆形

**🧟‍♂️ Code**

```html
<svg width="400" height="300" viewBox="0, 0, 400, 300">
  <circle cx="100" cy="60" r="50" fill="red"/>
</svg>
```

$tips:$ `fill` 填充色

**🧟‍♂️ Image**

![line5.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c9eae9abf20e404c94d02fd56c25e01f~tplv-k3u1fbpfcp-watermark.image)

**ellipse** 绘制椭圆

**🧟‍♂️ Code**

```html
<svg width="400" height="300" viewBox="0, 0, 400, 300">
  <ellipse cx="100" cy="60" rx="80" ry="50" fill="red"/>
</svg>
```

**🧟‍♂️ Image**

![line6.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5b559ee89df34b72858037ed061e18ec~tplv-k3u1fbpfcp-watermark.image)

**text** 绘制文本

**🧟‍♂️ Code**

```html
<svg width="400" height="300" viewBox="0, 0, 400, 300">
  <text x="100" y="60" stroke="red">我是绘制的文字</text>
</svg>
```

$tips:$

`stroke` 描边色

`style -> text-anchor` 对齐方式, 默认`middle`, 可选`start、middle、end`

**🧟‍♂️ Image**

![line7.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a6d0921629384ad4bca4ed5a60841c12~tplv-k3u1fbpfcp-watermark.image)

**line** 绘制直线

**🧟‍♂️ Code**

```html
<svg width="400" height="300" viewBox="0, 0, 400, 300">
  <line x1="100" y1="60" x2="300" y2="10" stroke="red" stroke-width="2"/>
</svg>
```

$tips:$ `stroke-width` 描边宽度

**🧟‍♂️ Image**

![line8.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/11079caacfc94940a964932aab515caa~tplv-k3u1fbpfcp-watermark.image)

**polyline** 绘制折线

**🧟‍♂️ Code**

```html
<svg width="400" height="300" viewBox="0, 0, 400, 300">
  <polyline points="30,140 100,60 300,10 350,50" fill="none" stroke="red" stroke-width="2"/>
</svg>
```

$tips:$ 记得`fill`给`none`, 否则路径部分会被填充哟

**🧟‍♂️ Image**

![line9.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ad4e71fd41d44164a18f713fc8e44188~tplv-k3u1fbpfcp-watermark.image)

**path** 路径

**🧟‍♂️ Code**

```html
<svg width="400" height="300" viewBox="0, 0, 400, 300">
  <path d="M 20,20 L 100,60 L 20,100 L 60,60 Z" fill="red"/>
</svg>
```

**🧟‍♂️ Image**

![line10.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8bb6684f8dfb4aef963d678d1d69bf62~tplv-k3u1fbpfcp-watermark.image)

**g** 分组 👉 将标签进行分组, 便于归类或复用

**use** 复制

**🧟‍♂️ Code**

```html
<svg width="400" height="300" viewBox="0, 0, 400, 300">
  <path d="M 20,20 L 100,60 L 20,100 L 60,60 Z" fill="red"/>
  <use href="#arrow" x="200" y="0" />
</svg>
```

**🧟‍♂️ Image**

![line11.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bbdfd9b8c29a4b769753cfd423f4d81e~tplv-k3u1fbpfcp-watermark.image)

**defs** 自定义图形

**🧟‍♂️ Code**

```html
<svg width="400" height="300" viewBox="0, 0, 400, 300">
  <defs>
    <path id="arrow" d="M 20,20 L 100,60 L 20,100 L 60,60 Z"/>
  </defs>
  <use href="#arrow" x="200" y="0" fill="red" />
</svg>
```

**🧟‍♂️ Image**

![line12.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/82d22aceae034057846eca4ed807f839~tplv-k3u1fbpfcp-watermark.image)

**层级关系**

**🧟‍♂️ Code**

```html
<svg width="400" height="300" viewBox="0, 0, 400, 300">
  <rect x="0" y="0" width="30" height="30" fill="purple"/>
  <rect x="20" y="5" width="30" height="30" fill="blue"/>
  <rect x="40" y="10" width="30" height="30" fill="green"/>
  <rect x="60" y="15" width="30" height="30" fill="pink"/>
  <rect x="80" y="20" width="30" height="30" fill="red"/>
</svg>
```

**🧟‍♂️ Image**

![line13.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/153c305511d24a5485a62df159e053fe~tplv-k3u1fbpfcp-watermark.image)

## ✍️绘制画布

前置知识准备的差不多了，伙计们，进入正题了❗️


![other19.gif](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/282649bb2598405ca1bf590dc9dded44~tplv-k3u1fbpfcp-watermark.image)

$First$ $of$ $all$, 我们先把$html$结构建出来

```html
<div id="line"></div>
```

$Secondly$, 我们用`d3`在这个`div`中创建出我们的画布, 宽高为`400✖️800`

* $d3$为链式结构

* `select` 选择元素 (选$id$)

* `selectAll` 选择全部元素 (选元素, 选类)

* `append` 追加元素

* `attr` 添加属性

```js
const width = 800
const height = 400

d3.select('#line')
  .append('svg')
  .attr('width', width)
  .attr('height', height)
```

## ✍️绘制坐标轴

### 比例尺

在开始比例尺之前，我们先来看几个比较好用的函数:

* `d3.max(arr)` return $max$

* `d3.min(arr)` return $min$

* `d3.extent(arr)` return $[min, max]$

本文所用到的为线性比例尺`scaleLinear`

❓ $what$ $is$ 线性比例尺

👉 将一个连续的区间，映射到另一区间 (`domin` 映射到 `range`)

![d3line1.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9ef53dd781c2482db5fe4a298a301cf2~tplv-k3u1fbpfcp-watermark.image)

**翠花, 上代码**

```js
👉 xScale
d3.scaleLinear() // 创建线性比例尺
  .domain([ // domin数据 [x.min, x.max]
    d3.min(xData),
    d3.max(xData)
  ])
  .rangeRound([0, width]) // range数据 [0, width]
```

### xAxis绘制

接下来，我们用`axisBottom`创建一个向下的坐标轴，并通过`scale`调用设置好的比例尺

```js
👉 xAxis
d3.axisBottom().scale(xScale)
```

然后, 通过`call`方法填充至画布上

```js
svg.call(xAxis)
```

至此, 一个粗糙的x轴就画好了, 接着奏乐接着舞

![d3line3.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/838ce401b0f542b88e5596b0bcb64e4e~tplv-k3u1fbpfcp-watermark.image)

有没有发现什么不对劲:

* 右侧的$100$惨遭截肢

* 坐标轴占满了整个画布, 丝毫没有美感

* 且$x$轴不应该在最下面吗, 左侧也要给它的好基友$y$轴留位置

![other20.jpeg](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/054e54849b444154aea64dfe0f7d314e~tplv-k3u1fbpfcp-watermark.image)

$1, 2$的核心问题就是没有留白, 既然要留白, 加`padding`就完事了; 而$3$一个`translate`就可以搞定

```diff
const padding = { top: 30, right: 30, bottom: 30, left: 30 }

👉 xScale
- .rangeRound([0, width])
+ .rangeRound([0, width - padding.left - padding.right])

👉 svg
svg
  .append('g')
  .attr('transform', `translate(${padding.left}, ${height - padding.bottom})`)
  .call(xAxis)
```
**🧟‍♂️ Image**

![d3line4.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b17b756bea3c426ab3dacadc709999af~tplv-k3u1fbpfcp-watermark.image)

**No.1**

* `axis.tickValues([...arr])` 用于指定坐标轴显示的值

* `axis.tickFormat()` 格式化坐标轴数据

$eg:$ 坐标轴数据按千分符形式格式化  `axis.tickFormat(d3.format(",.0f"))`, 其中`.0f`为不格式小数部分

**No.2**

第二点则可理解为给左右两端各补一条数据, 然后对两点连线. 而要补数据, 就要拟定补多少, 我们引入一个份数的概念, 将1份定义为总长度的 $ 1/dataset.length * 2 $, 最小为 $ 1/10 $.

```diff
// 份数计算
+ const length = dataset.length * 2
+ const partDistance = (d3.max(xData) - d3.min(xData)) / (length > 10 ? 10 : length)

.domain([
-   d3.min(xData),
-   d3.max(xData)
])

.domain([
+  d3.min(xData, item => {
+    return item - partDistance
+  }),
+  d3.max(xData, item => {
+    return item + partDistance
+  })
])
```

**No.3**

上文 “浅谈SVG中” 我们已经对 `defs`、`g`、`path`、`use` 分别做了讲解.

翠花, 上代码

```js
const arrowPath = 'M4,4 L20,12 L4,20 L8,12 Z'
const axisColor = '#fff'
const arrowOffsetDistance = 12

svg
  .append('defs')
  .append('g')
  .attr('id', 'arrowX')
  .append('path')
  .attr('d', arrowPath)
  .attr('fill', axisColor)

svg
  .append('use')
  .attr('href', '#arrowX')
  .attr('x', width - padding.right - arrowOffsetDistance)
  .attr('y', height - padding.bottom - arrowOffsetDistance)
```

`M4,4 L20,12 L4,20 L8,12 Z`

* `d3.path()` 创建path路径

* `moveTo(x, y)` M

* `lineTo(x, y)` L

* `closePath()` Z

故也可根据方法动态生成

```js
let arrowPath = d3.path() // M4,4 L20,12 L4,20 L8,12 Z
arrowPath.moveTo(4, 4)
arrowPath.lineTo(20, 12)
arrowPath.lineTo(4, 20)
arrowPath.lineTo(8, 12)
arrowPath.closePath()
```

**No.+∞**

为了使坐标轴更美观, 我们来随意点缀几笔, 最终呈现效果如下:

![d3line5.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1bf4d065cb8b4735a4739640a5878883~tplv-k3u1fbpfcp-watermark.image)

$tip:$ 通过`select / selectAll`去选中元素更改对应属性 (或$style$或$svg$的图形属性)

补其它的好基友$y$轴斯密达


![d3line6.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9e7e793ec16a4053bd46c752d35ae164~tplv-k3u1fbpfcp-watermark.image)

### marker标记

用于对图形做元素追加, 我们的坐标轴就非常符合这个特征

* `markerWidth / markerHeight` 宽 / 高

* `refX / refY` $x / y$ 轴偏移量

* `markerUNits` 是否允许marker随所连接图形的缩放而跟随缩放, 默认strokeWidth(缩放), 可选userSpaceOnUse(不缩放)

* `orient` 旋转角度, 默认$auto$, 可指定具体旋转度数

❓ 如何对以绘制的图形做追加

👉 对以绘制的图形添加以下属性: `marker-end="url(#id)"`, 可选 [marker-start、marker-mid、marker-end]

**特别注意**: marker可以理解为作用于点, 所以`marker-mid`对两点间的连线是没有效果的, 要至少三个点才能产生效果

**🧟‍♂️ Code**

```html
<svg width="400" height="300" viewBox="0, 0, 400, 300">
  <marker id="arrow" markerWidth="12" markerHeight="12" refX="6" refY="6" orient="30deg">
    <path d="M2,2 L10,6 L2,10 L4,6 Z" fill="red" />
  </marker>
  <line x1="100" y1="60" x2="300" y2="60" stroke="red" stroke-width="2" marker-end="url(#arrow)" />
</svg>
```

**🧟‍♂️ Image**

![line15.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0df60bde939840bd8c9945e99f93cd78~tplv-k3u1fbpfcp-watermark.image)

两种写法的对比如下:

![d3line7.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9bac93a3ca614ac395a602792220d720~tplv-k3u1fbpfcp-watermark.image)

## 绘制坐标点

坐标点❓画个圈圈诅咒你 (画圆呀)

空心点❓人体描边大法 (stroke-width 你值得拥有)

映射到坐标轴❓比例尺 *之* 炼金术 (按比例尺映射回去)

**🧟‍♂️ Code**

![d3line8.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dfabd1e406834a7aad0316db69517869~tplv-k3u1fbpfcp-watermark.image)

**🧟‍♂️ Image**

![d3line9.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aef3600204f24de6ae736e533db4921b~tplv-k3u1fbpfcp-watermark.image)

## 绘制折线

点、线、面, 有了点我们开始连线. 用什么呢? 我建议大家用`path`(“绘制坐标轴”处已说明用法), 但我选择用`line`, 没什么原因, 任性而已, 不服你打我呀!

![other21.jpg](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d6d993515c6745c98f2c7e9fe7600e2e~tplv-k3u1fbpfcp-watermark.image)

**💻 知识点**

`d3.line().defined()` 当前点是否与其相邻点进行连线

以点画线, 可点真的全了吗? 起点在哪里呀, 终点在哪里, 在那小朋友的眼睛里? 我们按前面计算的份数补全下数据, 然后绘图.

```js
// 折线数据处理
let newDataset = []
newDataset.push({
  xValue: dataset[0].xValue - partDistance,
  yValue: dataset[0].yValue,
  filled: true
})

newDataset.push(...dataset)

newDataset.push({
  xValue: dataset[dataset.length - 1].xValue + partDistance,
  yValue: dataset[dataset.length - 1].yValue,
  filled: true
})
```


![d3line10.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/81280eaec5cb49e080226bd1dcbb5bfd~tplv-k3u1fbpfcp-watermark.image)

线覆盖了空心点 ❓ 根据量子力学 - $svg$层级顺序定律, 一定是图层顺序搞反了. 换下代码顺序, 完美解决.

$x$坐标相同的连线 ❓ 有没有注意到上面的`defined`, 不过, 我是要对其x坐标相同的点不进行连线, 而不是放空这个点, 还它自由. 克隆一个, Perfect, 完美解决问题. 而至于克隆哪个点, 随心情就好, 你说我两个都想要, 拖出去斩了.

```diff
// 折线数据处理
- newDataset.push(...dataset)
+ dataset.forEach(item => item.filled ? newDataset.push(item) : newDataset.push(...[item, item]))
```

**🧟‍♂️ Code**

![d3line12.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/382f76894e274c61bc6e42a87edc72db~tplv-k3u1fbpfcp-watermark.image)

**🧟‍♂️ Image**


![d3line11.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/70f27cf8437d4cce8dd61695d9b1ee0f~tplv-k3u1fbpfcp-watermark.image)

## 绘制坐标点到坐标轴的虚线

**stroke-dasharray** : 用于绘制虚线, 每绘制$x$个像素点, 则空余$y$个像素点

**🧟‍♂️ Code**

```html
<svg width="400" height="300" viewBox="0, 0, 400, 300">
  <line x1="50" y1="50" x2="350" y2="50" stroke-dasharray="3" fill="none" stroke="red" stroke-width="3"></line>
  <line x1="50" y1="180" x2="350" y2="180" stroke-dasharray="6, 18, 4, 12" fill="none" stroke="red" stroke-width="3"></line>
</svg>
```

**🧟‍♂️ Image**

![line16.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/eb0a1fcb690e41b39f6b1986d5e0a0ad~tplv-k3u1fbpfcp-watermark.image)

找到点, 连上线, 我们的虚线就画好了

**翠花, 上酸菜**

![d3line13.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/41f2cbcbd278487bb74ec43c7ffbc87b~tplv-k3u1fbpfcp-watermark.image)

**🧟‍♂️ Image**

![d3line14.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6a900b355ad84bc8bc82770bf95e9b71~tplv-k3u1fbpfcp-watermark.image)

## Tooltip

在构思`tooltip`之前, 先抛出几个问题

❓ `tooltip`显示的是什么

👉 对应点的坐标

❓ hover的区域是什么

👉 整个坐标轴

❓ 显示的位置在哪里

👉 对应点的旁边

❓ 还有需要注意的吗

👉 不要超出边界范围

![other13.gif](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/16e773c048974b568348359aa3ed9b55~tplv-k3u1fbpfcp-watermark.image)

**我们来总结下**

$hover$的是整个区域 👉 一个覆盖整个坐标轴的$rect$, 然后对此进行事件处理

$tooltip$ 👉 一个显示在对应点旁边的小$div$

$hover$时找到**x=c**上对应的点的坐标, 对$x$轴和$y$轴连接虚线, 并在点旁显示$tooltip$

**躁动起来**

先来`append`一个`rect`, 然后对齐做事件处理. 这里用到我们熟悉且性感的老朋友们就可以了, 下面有请他们闪亮登场:

```js
  svg
    .append('rect')
    .attr('width', areaWidth)
    .attr('height', areaHeight)
    .style('fill', 'none')
    .style('pointer-events', 'all')
    .style('cursor', 'pointer')
    .attr('transform', `translate(${padding.left}, ${padding.top})`)
    .on('mouseover', mouseOver)
    .on('mouseout', mouseOut)
    .on('mousemove', mouseMove)
```

接下来我们把$tooltip$和其到$x$轴与到$y$轴的虚线绘制出来.

这时会有这样一个疑惑, 我还没有计算位置, 怎么绘制, 看官莫急, 请看下文

![other22.jpeg](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/136481625a0a4b458521886d21956ee9~tplv-k3u1fbpfcp-watermark.image)

`mouseMove`这个小家伙可以帮我们拿到所在点的`offsetX`, 那找点的问题就转变成了已知$x$求$y$, 进而转变成求关联$x,y$的函数表达式.

已知首段和尾段$y$恒定($y=c$), 中间各段为:

$$
(x - x1) * (y2 - y1) = (x2 - x1) * (y - y1)
$$

### 扫盲

拿起机关枪, 对准盲点先来一波扫射

**scaleLinear -> invert**: 反向映射 (根据给定的位于 `range` 中的值返回对应的位于 `domain` 的值)

```js
const xInvert = xScale.invert(d.offsetX - padding.left)
```

**d3.bisector()**: 平分线

为了理解这个概念, 我们先来讲讲它的亲戚`bisectLeft`、`bisectRight`

**d3.bisectLeft(arr, x)**

其中$arr$为被插入的数组(需排序), $x$为要插入的值, 返回值为插入位置. 若$x$已在数组中, 则插入至相同条目的最左面.

```js
const arr1 = [2, 3, 5, 6, 7]
d3.bisectLeft(arr1, 4) // return 2

const arr1 = [2, 3, 3, 6, 7]
d3.bisectLeft(arr1, 3) // return 1
```

我们说回`bisector`, 这是个啥呢, 它和上面其实是一样的, 我们实际开发中基本都是复杂的数组结构, 我们可以根据`bisector`来指定与$x$比对的值.

```js
const bisect = d3.bisector(d => d.xValue).right
const xInvert = xScale.invert(d.offsetX - padding.left)
const i = bisect(dealDataset, xInvert)
```

这样就求出了当前所在点的区间, 进而知道了要用哪段函数求$y$

![other14.jpg](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bc7d4764c6db41b4adf47aefc4c65c44~tplv-k3u1fbpfcp-watermark.image)

**getBoundingClientRect & getBBox**

`selection.node().getBoundingClientRect()` 获取HTML元素的相关属性

展示数据如下:

![d3line15.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4ce761a6facb47d195853224f89dc3e1~tplv-k3u1fbpfcp-watermark.image)

`selection.node().getBBox()` 获取$svg$元素的相关属性

书接上文, 已知函数和点$x$, 即可求点$y$, 进而画出该点到$x$轴和$y$轴的虚线. 然后我们对 $tooltip$ 填充展示内容, 为防止边界点超出画布, 根据 $getBoundingClientRect$ 做边界值的位置调整.

**🧟‍♂️ Code**

![d3line16.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/af44294ac3fb48f0b4c028e79dde52ed~tplv-k3u1fbpfcp-watermark.image)

**🧟‍♂️ Image**

![d3line17.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f2c4d5aad37848018932ac33e9fe9cd3~tplv-k3u1fbpfcp-watermark.image)

大功告成, 完结撒花🎉

**完整代码连接** [传送门 Github - ajun568](https://github.com/ajun568/d3-piecewise-line)

![other24.jpg](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d281439caa5349d9a52659e280da542d~tplv-k3u1fbpfcp-watermark.image)


## 参考🔗链接

[D3官网](https://d3js.org/)

[[MDN] SVG element reference](https://developer.mozilla.org/en-US/docs/Web/SVG/Element)

[[Scott MurrayAn] SVG primer](https://alignedleft.com/tutorials/d3/an-svg-primer)

[[阮一峰] SVG 图像入门教程](https://www.ruanyifeng.com/blog/2018/08/svg.html)

[[张鑫旭] 理解SVG viewport,viewBox,preserveAspectRatio缩放](https://www.zhangxinxu.com/wordpress/2014/08/svg-viewport-viewbox-preserveaspectratio/)

[[彦子] Marker特性——在元素中引用marker
](https://www.w3cplus.com/svg/svg-markers.html)

[[Terry] D3：什么是平分线？](https://www.javaer101.com/article/2678931.html)

