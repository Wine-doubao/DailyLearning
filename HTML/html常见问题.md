前端即使用web技术栈解决多端图形界面人机交互问题。

前端应该关注功能、美观、无障碍（是否对所有用户都可以使用）、安全、性能、兼容和体验等方面。

HTML即HyperText Markup Language，超文本标记语言。超文本指的是可以包含图片、标题、链接和表格等不同格式的文本，使用标签来标记这些格式。

## HTML5的新特性

1. `语义化标签`8个：header、nav、footer、section、article、aside(侧边栏内容)、summary、detailes、dialog(对话框)
2. `增强型表单`：
   - 新的input输入类型：color、date、datetime(UTC时间，已废弃)、datetime-local(日期时间不包括时区)、email、month、number(只能输入数字)、range、search、tel、time(时间)、url、week(年和周)
   - 新的表单元素：datalist(输入域选项列表)、keygen(验证用户)、output(不同类型输出)
   - 新的表单属性：placehoder、required(boolean)、pattern(正则)、min/max、step(合法数字间隔)、height/width(image宽高)、autofocus(boolean)、multiple(boolean)
3. `视频和音频`：video、audio
4. `canvas`绘图：标签只是图形容器，必须用脚本来绘制图形
5. `SVG`绘图：可伸缩的矢量图形，使用XML描述2D图形
6. 地理定位`Geolocation`
7. `拖放API`
8. `WebWorker`：运行在后台的JavaScript，独立于其他脚本，不会影响页面的性能
9. `WebStorage`：本地存储用户的浏览数据，不影响网站的性能
10. `WebSocket`：在单个TCP链接上进行全双工通讯的协议

[参考文章](https://juejin.cn/post/6844903829679390728)

### 对语义化的理解

即HTML中的元素、属性及属性值都拥有某些含义，使页面内容结构很清晰。如center标签只是起一个居中的作用，这完全可以使用css样式做到，没有太大的语义作用，所以被废除了。

为什么需要语义化？

1. 增加代码可读性、可维护性
2. 让搜索引擎的爬虫程序或屏幕阅读工具迅速得到有用信息，利于SEO
3. 提升无障碍性
4. 面向未来的HTML，浏览器可能提供更丰富的支持

## DOCTYPE的作用是什么

DOCTYPE 声明位于文档的最前面，在 HTML 标签的前面，**用于告知浏览器的解析器当前文档的类型规范，以什么模式来解析文档**。一般指定后会以**标准模式**来进行文档解析，否则以兼容模式解析。

- `标准模式`的渲染方式和 JS 引擎的解析方式都是以该浏览器支持的最高标准运行
- `兼容模式`中，页面以宽松的向后兼容的方式显示，模拟老式浏览器的行为以防止站点无法访问

因为 HTML5 之前的文档都是基于 SGML 的，所以需要对 DTD 进行引用，才能告知浏览器文档所使用的文档类型。HTML5 不基于 SGML，因此不需要指定 DTD 文档，但需要 DOCTYPE 来规范浏览器的行为。

### SGML 和 DTD

- SGML：标准通用标记语言，是一种定义电子文档结构和描述其内容的国际标准语言。
- DTD：一组机器可读的规则，定义了XML或HTML的特定版本中所有允许元素及他们的属性和层次关系的定义。DTD是对 HTML 文档的声明，在解析网页时，浏览器将使用这些规则检查页面的有效性并采取相应的措施。

## meta元素都有什么

文档级元数据元素：用来构建HTML文档的基本结构，提供了网站的信息，对网页是否能被搜索引擎检索，和在搜索中的排名起着关键性的作用。

meta元素主要的四个属性：

- charset：声明页面的字符编码，UTF-8
- content: 通常配合name或者http-equiv使用
- http-equiv：设置的都是与http请求头相关的信息，也就是说浏览器在请求服务器获取html时，服务器会将html中设置的meta放在响应头中返回给浏览器。
- name：用于定义页面的元数据，不能与charset、http-equiv一起使用，通常是和content配合使用

```html
<head>
  <title>示例</title>
  <meta http-equiv="content-type" content="text/html; charset=utf8">
  <!--如果使用content-type属性，其值唯一-->
  <meta http-equiv="expires" content="过期时间">
  <meta http-equiv="refresh" content="秒数 url=地址">
  <meta http-equiv="window-target" content="_top">
  <!--强制页面在当前窗口以独立显示，可以防止别人在框架中调用自己的页面。-->
	<meta http-equiv="pragma" content="no-cache">
	<!--禁止浏览器从本地计算机的缓存中访问页面内容-->


  <meta name="keywords" content="描述网站内容的关键词，以逗号隔开，用于SEO搜索">
  <meta name="application name" content="当前页所属Web应用系统的名称">
  <meta name="description" content="当前页的说明">
  <meta name="author" content="当前页的作者名">
  <meta name="copyright" content="版权信息">
  <meta name="renderer" content="renderer是为双核浏览器准备的，用于指定双核浏览器默认以何种方式渲染页面">
  <meta name="viewreport" content="提供有关视口初始大小的提示，仅供移动设备使用">
</head>
```



## src 和 href 的区别

- href指向网络资源位置，`建立当前文档和资源的连接`，一般用于超链接
- src将资源放入当前的文档，在请求src资源的时候会将指向的资源`下载并应用到文档中`，比如说js、图片等等元素

需要引入到页面使用的用src；链接到外部页面的用href。

## link 标签和 @import 的区别

1. @import 是 css 提供的语法规则，只有导入样式表的作用；link 属于 html 标签，不仅可以加载 CSS 文件，还可以定义 RSS（简单信息聚合）、rel 连接属性、引入网站图标等
2. 页面被加载时，link 会和 html 同时被加载；@import 引入的 CSS 将在页面加载完毕后被加载
3. @import 是 CSS2.1 才有的语法，只有 IE5+ 才能识别；而 link 是 HTML 标签，不存在兼容性问题
4. JavaScript 能控制 dom 去改变 link 标签引入的样式，而控制不了 @import 的样式
5. link 引入的样式权重高于 @import
5. 在原生开发中不建议使用@import，如果加载a.css之后里面又需要加载b.css，存在一个串联加载的问题，会影响性能
5. 如果前端工程化工具中使用@import，它打包时会将相应文件一起打包在一块，不存在性能问题

注：@import 的最优写法为 `@import url(style.css)`。@import 适用于引入公共基础样式或第三方样式，link 适用于自己写的或需要动态修改的样式。

[参考文章](https://www.cnblogs.com/li-jp/p/11636684.html)

## SEO（搜索引擎优化）

1. 网站结构布局优化
   - 提倡扁平化结构，即让爬虫只要跳转3次就能到达网页任何一个页面
   - 控制首页链接数量，不要太少也不要太多
   - 导航优化，图片标签添加`alt`和`title`属性
2. 网页代码优化
   - 突出重要内容
   - 语义化书写HTML代码，用正确的标签做正确的事情
   - 重要内容不要用JS输出，因为爬虫不会读取JS中的内容

优化的目的：提高网站的权重，增强搜索引擎友好度，以达到提高排名，增加流量，改善（潜在）用户体验，促进销售的作用。

## iframe

优点：

1. 重载时不用重载整个浏览器窗体，只需要重载其中一个小页面
2. 实现简单

缺点：

1. 不安全
2. 对SEO不友好
3. 浏览器无法使用后退按钮
4. 增加服务器的http请求，造成拥塞
5. 手机端无法显示
