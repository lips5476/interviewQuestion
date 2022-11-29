## 选择器优先级
  import>行内>id>class>标签>通配符
  样式表来源不同时    内联>内部》外部》浏览器用户自定义》浏览器默认
## 会继承的样式
  font- line- color-   input是不会继承样式的
## link和import的区别
   link引入样式会和页面同时加载 @import会在页面完全加载完再载入
   import低版本不支持   link支持js控制dom修改样式  
##  伪元素和伪类的区别
  伪元素是为了在某个元素前后插入元素由不会在render树里显示出来
  伪类是为了给元素添加一些特定状态下方法
## requestAnimation 
   h5新增的动画api  因为setTimeout它的延时并非精准的因此用于动画会有卡顿  requestAnimation
   会在浏览器执行下一次重绘之前调用指定的回调函数更新动画  它会紧随着浏览器刷新频率
##  css3有哪些新特性
    圆角 阴影 文字阴影 渐变旋转
## 常见图片格式
    bmp无损   gif  svg  png  webp
##  css性能优化
    css压缩  减少使用link    
##  单行文本隐藏  多行文本隐藏
overflow: hidden;               
text-overflow: ellipsis;            
white-space: nowrap;  


overflow: hidden;            // 溢出隐藏
text-overflow: ellipsis;     // 溢出用省略号显示
display:-webkit-box;         // 作为弹性伸缩盒子模型显示。
-webkit-box-orient:vertical; // 设置伸缩盒子的子元素排列方式：从上到下垂直排列
-webkit-line-clamp:3;        // 显示的行数
## less sass的优点
   解构清晰 便于扩展  方便继承
## 如何判断一个元素到达可视区
  img.offsetTop < window.innerHeight + document.body.scrollTop;
## z-index属性在下列情况下会失效
  父元素为relative  元素本身无position属性时  设置了z-index还设置了浮动
## rem使用单位
   相对于根元素的font-size倍数  可以利用rem实现简单的响应式布局 利用html文字大小和屏幕的比值设置font-size来实现元素跟随屏幕变化而变化
## flex 1表示
   flex-grow:1  flex-shrink:1  flex-basis:auto
   flex-grow:0  flex-shrink:1  flex-basis:auto
   flex-grow定义该元素在剩余空间里所占的比例  flex-shrink  定义该元素在剩余空间里缩小的比例
   flex-basis定义该元素在主轴空间中本身占据多少比例  
## 清除浮动的本质
   父元素无高度  子元素浮动  父盒子的高度不会被子盒子撑开  父盒子会消失跑到子盒子下方
   方案  父盒子加一个空div 然后clear:both 
         父盒子overflow hidden  或者display:flow-root
## bfc
  块级格式化
  子元素的margin不会跑到父元素外面
  让子元素的基线不以最后一行内容为准而是以margin-box为准
  会包裹住所有后代中浮动元素  也会变窄避开所有其他浮动元素
  overflow:hidden    dispaly:flow-root dispaly:inline-block
## clear 清除浮动的原理
   用来定义哪个方向不可以有浮动元素
##  stick
   当页面滚动在目标元素范围内时  stick元素行为模式如position relative   超出时 fixed
##  flex  属性含义
   flex-wrap属性定义，如果一条轴线排不下，如何换行。
   flex-direction属性决定主轴的方向（即项目的排列方向）。
   flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。
   justify-content属性定义了项目在主轴上的对齐方式。
   align-items属性定义项目在交叉轴上如何对齐。
   align-content属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。
   order属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。
   align-self属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。


