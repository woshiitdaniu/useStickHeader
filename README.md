好久没有写过博文做分享了，感觉码文章的逻辑已经赶不上自己码代码的速度，不过偶然发点有趣话题，一起用代码或者算法的角度去玩玩也挺有趣的。

不知道大家是如何去带自己的组员上道的，尤其是刚出校门不久的小学弟，如何让他心甘情愿的做事又能快乐去编码，今天就遇见一个很有普适性的事情，值得跟大家分享分享的。

事情是这样，产品经理从用户那获得了一个需求，与其说是需求，倒不如说是吐槽。

用户说以前的系统长table的表头都是可以固定的，这样我滚动滚动条也可以很方便的知道哪些值对应哪些字段，不然我现在一下子按200条去看滚到低哪里还记得这列是啥内容啦？

产品经理觉得这个吐槽确实是自己的失责，这种明显提升用户满意度需求都没满足。只见他为难的说，这确实是我们的问题，前期是因为需求太集中，人力都十分有限，只能优先排期重要的需求，先实现核心功能了。这样吧，我找前端组长讨论下，让他明天做出效果你看看。

于是我就被叫过去，问这个功能实现复杂性....领完这种需求我觉得可以让新招不久的小学弟试试水。

嘻嘻，你们懂得...小明啊，你前面的项目代码看得怎么样啦？有没有什么问题？...我这有个小小需求，想安排你做，有没有信心做好？我们现在项目的框架是react的antd-pro全家桶，UI主要是用的antd，版本是2.x.x，需求是这样的：选一个现有模块中的一个table列表页试点，让table的表头在有滚动条的情况下滚动能固定住。明天下午将这个功能做好然后发体验是否OK？只见他看了一下说明以及已有的页面，很用力的点了点头，可以的。

![图片1.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/52bd3a52a7704c9faebf3ec1928fbedf~tplv-k3u1fbpfcp-watermark.image?)

![图片2.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/41a54c22e24b4f47b23f74eddad2346d~tplv-k3u1fbpfcp-watermark.image?)

当天下午他便高兴的过来跟我说，老大你来验收下上午你说的那个需求。我挺惊讶的，心想这小火鸡可以呀，领悟和动手能力都挺强。

于是我打开界面选择按50条进行列表展示，并滚动滚动条，发现头部确实能固定了

![图片3.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1ab5f784833f42ca8f30f568a8719d5e~tplv-k3u1fbpfcp-watermark.image?)

咋一看表头固定这个功能确实好像实现了，在他让我看他代码前我还是发现了多少问题，比如表头固定后字段之间错位了，而且拖动横轴滚动条，顶部表头原来可以跟随滚动现在也被固定住了...

他也许还沉醉在等我表扬他的幻想中，立刻打开了他写的代码。列位请围观：代码如下↓

![图片4.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/be02a25c2043478c8eb92a8f48f17236~tplv-k3u1fbpfcp-watermark.image?)

想必会引来大牛们一顿啪啪，这都写的啥...一坨坨的。他自豪的跟我说，一开始他想直接去UI组件库文档找看看有没有属性直接拿来用，但是发现那个版本的antd只有table内的滚动才可以设置头部固定，外层滚动还没有提供api，只能自己动手实现了，他开始设定了两种方案一个是使用position:absoluted另一个是fixed，后面他用了fixed，用了table里的antd提供的class来计算设置fixed的边界，后面都觉得不方便而且也不灵活，然后就想到在table上面给个空标签做锚点不设置高度，它的作用就是用来计算滚动边界的 scrollTops>far,嘿嘿这锚点一说还真有点眼前一亮，虽说增加了一个空标签，但是极大方便了距离的获取，点个赞。

细看下，他还是思考了的，![]() offsetTop的获取并没有放在callback函数里，否者会增加不少的性能压力（引起重新渲染了）

![图片5.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/58e15eaea9bb49aca543b274d6bb8f4f~tplv-k3u1fbpfcp-watermark.image?)
 
但是思考得不深，而且代码的写法有非常大的问题。这里先不说，看了代码后我问他，小明你当时写完都测试过吗各种场景各种影响下，比如我滚动横轴，比如头部是否对齐？只见他被我这样一问，脸立即红了起来，说了句这种简单的功能应该不会有什么问题的吧。看着他就想起自己刚毕业工作时候的样子，充满自信甚至还有点自负，以为什么事情都是一次性就能做到位，简单的功能没必要每个场景都去测试，没道理有问题的。就是这种思想让自己后面吃了不少的亏。

在他不太相信有bug的时候我打开了他试点的列表页，并滚动了x滚动条，果然不出所料。头部虽然使用fixed固定定位，然而表头字段却没有跟着滚动。同时表头后面也错位了。看完bug现象后，他立刻就知道了问题所在，错位是因为加了fixed后，表头脱离了当前文档流成了BFC元素，于是就没有了width，只见他取table宽度加上后表头错位问题解决了，而不能滚动也是因为这个原因，他接着加了overflow:scroll属性并且隐藏了滚动条样式，确实也能跟着滚动了。

![图片6.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/46adf3e7ed6247c688d68312bbe8109a~tplv-k3u1fbpfcp-watermark.image?)

看着他这样式写得越来越长，我试着点下他，于是跟他说，如果我后面要你在加更多的样式，你都这样写一起会不会太难看啦，而且一个callback函数写那么多功能逻辑是不是不符合函数的单一原则？另外，你将获取offsetTop的代码写在了useEffect里假如我的搜索条件增加了那你获取的高度是不是就不准了？

比如点击展开查看更多的搜索条件![]()

![图片7.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e8502746421f47a6b6ba0ef42bc19184~tplv-k3u1fbpfcp-watermark.image?)

听完我这句话，他马上去验证果然高度计算错位了

![图片8.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c49a7445c81141b49f2a277970d16954~tplv-k3u1fbpfcp-watermark.image?)

同样的一顿修改 代码如下：

![图片9.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4f07cc4ae2674828b9582dde935d9681~tplv-k3u1fbpfcp-watermark.image?)

这次他使用了useEffect的第二个参数监听了查询参数的展开与收起按钮的状态然后重新计算了offsetTop,同时也使用了useCallback来缓存获取的逻辑，这点可以说开始上道了。

改完这些后他开始不放心的让我再给他测试一下其他可能存在的场景，其实看了他边界处理逻辑我就有种感觉就是当列表高度刚好等于边界值的时候 可能会出现问题。果然，当我们设置为20条每页时，表头出现了上下跳动的怪现象，我让他分析下可能的原因，一开始他以为是因为浏览器或者是电脑原因，换了电脑之后问题依然存在，于是他在代码上console了下发现：

![图片10.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/59d2d825ccd5481798be087866548361~tplv-k3u1fbpfcp-watermark.image?)

列位看出是什么原因导致这种一正一负现象了么？聪明的你们一定想到了，没错就是因为表头设置了fixed后，table的高度突然变小了，从而使得整个页面高度也突然变小，滚动条的scrollTop-far就会变成负了，又因为是在变负的时候进入的if内，所以就会出现上下跳动了，找到问题原因那问题就针对性解决咯，只见小明在scrollTop>farl的条件内加了一句

antlayoutcontent.style.cssText = `height:${headerH}px;`，意图很明显就是想，既然是因为高度坍塌导致的那我就瞬间将高度补回去不就完啦？而补回去的高度就正好是表头的高度。然后不需要固定表头的时候又将高度置为0。

貌似这样之后功能基本就实现了，滚动时表头能正常固定在合理的位置且能自由左右跟随滚动，小明在测试完刚刚遇到的所有bug场景都觉得没有问题后正准备git push代码到gitlab时，我立马阻止了他，我说：小明，你这只是一个页面实现了表头固定，用户不能只在这个页面才能固定表头吧？这样，你将这个功能在所有页面都实现了，但是要注意实现方式...

小明貌似领会了我的意思，没错，就是需要将这个逻辑进行hooks化，方便被其他页面复用，又经过一个小时的改造，小明让我看了他最后写完的代码：代码如下↓

Hooks/useStickHeader/useStickHeader

![图片11.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/17e6e6f8818b4537a0a8dcaf7632fc69~tplv-k3u1fbpfcp-watermark.image?)

哟，这小伙还知道做了部分性能优化，比如使用了标记来消去固定后不必要的滚动逻辑，同时还利用了帧动画api requestAnimationFrame 来减轻浏览器主线程渲染压力。

requestAnimationFrame 会存在兼容性问题，对于我们主要是针对B端用户来说，兼容到IE9是需要的，因此我也要求他做了兼容处理

![图片12.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/42d1ebc401ff4cdbb01965d8f7f90056~tplv-k3u1fbpfcp-watermark.image?)

但是这样做相比于没有做之前到底优化了多少性能呢？我问小明，小明支支吾吾并没有一个准确的性能优化定量结果。于是我建议他在本地使用performance.timing测测前后对比，包括内存、cpu、fps、掉帧率等

过了一段时间后，他截了一个优化前的performance录屏结果给我看 见下图↓

![图片13.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3377867828c542e6bf5419f966ceab8b~tplv-k3u1fbpfcp-watermark.image?)

![图片14.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c66a0fc89bae4f54acfad44f43f95a7f~tplv-k3u1fbpfcp-watermark.image?)

从这个图片可以看到，优化前FPS有很多红块以及绿条间被切割了，细看一下切割处我们可以发现这个区域的页面显示是红色的，很明显页面没有渲染出来，也就是说这一段时间页面产生了卡顿！而且随着数据量越大，滚动越频繁，fps绿条被切割的次数越多，卡顿就越频繁，同时cpu使用来看正常情况下script和render都是有数据的，而且红块的地方基本是js在频繁处理，纯绿条处是纯render，可见js的执行一定会阻止页面的render的，结合本功能滚动来说，应该尽量避免在scroll监听回调函数中计算负责的内容，甚至可以像小明那样非必要的滚动就用个标记过滤掉回调逻辑。

再看看小明优化后的录屏结果

![图片15.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/edda2406bb6f464081fc372f0286362d~tplv-k3u1fbpfcp-watermark.image?)

![图片16.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b2964a5033f04a93af15ac1e4ca08d54~tplv-k3u1fbpfcp-watermark.image?)

以上是基于每页20-100条数据的情况

以下是每页500条数据的情况：

![图片17.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/682129983e8543d0ab3ed7d935c2e248~tplv-k3u1fbpfcp-watermark.image?)

![图片18.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f703c6d1b2ed4e77906149604e541022~tplv-k3u1fbpfcp-watermark.image?)

从上图可见如果table列表的数据量太大，即时是用了GPU也还是会很明显的感觉到卡顿的

因此建议用户200条以下查看了...为了保持流畅

嘿嘿，看完小明这分析，我觉得当初面试果然没看错人呀，只是觉得这个功能虽然已经从最初的页面逻辑抽离成了可供其他页面使用的hooks，但是还是有可以优化的地方的。

比如作为一个公共的hooks还需要用户在页面去写个锚点的空标签!显然这是不合理，万一用户忘记加那这个功能就无法实现了，应该要做到用户无感、不侵入已有页面逻辑等，

针对这个问题，小明又对这个hooks的使用方式做了修改,增加了一个自定义的pageTable组件，使用者只需要引入该组件就能实现无感引入表头固定了。
![图片19.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0b416fbd019d47c880c7200966b46975~tplv-k3u1fbpfcp-watermark.image?)

还有就是作为Hooks怎么也应该暴露出一些钩子函数或者是state让用户控制吧？比如让用户能监听固定瞬间或者是让用户自主控制是否需要固定等。而且文档注释等都需要进一步完善。

 

小明听完我的讲解后，终于知道自己问题所在了：

 

1. 考虑问题不全面，只为了实现表面的功能而去开发，缺乏测试思维和运维思维。

2. 缺乏前端工程化、组件化实践经验

3. 对开发的功能或者插件没有从性能角度主动分析，仅仅停留在功能实现

4. 缺乏主动性，遇到问题不能主动思考解决

5. 没有严格要求自己，包括代码的编写、逻辑处理、注释要求、开发规范等。

 

前端是个系统、繁杂的体系，里面充满了未知和值得探索的领域，自己作为了过来人，深深的知道只有遇到好的指路人 才能更好的成长，因此在自己不断学习进步的同时也会尽自己最大的努力去帮助新同事一起成长。感谢各位大佬看完这篇文章，一起努力吧
