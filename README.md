# css-secrets
《css揭秘》一书中的一些css技巧整理，其中也加入了一些其他的知识。

## 第一章之css编码技巧

#### 尽量减少代码重复

e.g 可替换背景色的按钮

```css
button {
  font-size: 125%;
  line-height: 1.5;  /*line-height没有单位时，是相对于font-size的倍数*/
  padding: .3em .8em;
  border: 1px solid rgba(0, 0, 0, 0.1);
  background: #58a linear-gradient(hsla(0, 0%, 100%, .2), transparent);
  border-radius: .2em;
  box-shadow: 0 .05em .25em rgba(0, 0, 0, 0.5);
  color: white;
  text-shadow: 0 -.05em .05em rgba(0, 0, 0, 0.5);
  cursor: pointer;
  outline: none;
}
```
其中，`hsla`分别表示：

    H：Hue(色调)。0(或360)表示红色，120表示绿色，240表示蓝色，也可取其他数值来指定颜色。取值为：0 - 360
    S：Saturation(饱和度)。取值为：0.0% - 100.0%
    L：Lightness(亮度)。取值为：0.0% - 100.0%
    A：Alpha透明度。取值0~1之间。

这样，只需要改变`background-color`即可得到不同颜色版本的按钮。

```css
button.cancel {
  background-color: #c00;
}

button.ok {
  background-color: #6b0;
}
```

* 代码易维护 vs. 代码量少

有时候这两者不可兼得。

e.g

```css
border-width: 10px 10px 10px 0;
```
上面的例子不易维护，但改成下面这样就易维护多了：
```css
border-width: 10px;
border-left-width: 0;
```

* currentColor

css有史以来第一个变量， 其用法如下：

```css
p{
  color: #e66665;
  margin-bottom: 1rem;
  font-size: 2rem;
  border-bottom: 1px solid currentColor;
}
```

上述代码实现的效果是：让p的border-bottom颜色与文本的颜色保持一致。currentColor会继承文本的颜色。

* 继承
inherit可以用在任何css属性中，它总是绑定到父元素的计算值（对伪元素来说，则会取生成该伪元素的宿主元素）。inherit对于背景色同样有用。

e.g
```css
div{
  position: relative;
  background: #f1d17f;
  width: 200px;
  border-radius: 5px;
  top: 10px;
  padding: 7px;
}
div::before{
  content: "";
  position: absolute;
  top: -.4em;
  left: 1em;
  padding: .35em;
  background: inherit;
  border: inherit;
  border-right: 0;
  border-bottom: 0;
  transform: rotate(45deg);
}
```
以上代码实现了一个提示框，`::before`实现了一个向上的三角形按钮，它的背景色继承了div的背景色。

#### 合理使用简写

```css
background: url(tr.png) no-repeat top right / 2em 2em,
            url(br.png) no-repeat bottom right / 2em 2em,
            url(bl.png) no-repeat bottom left / 2em 2em;
```

上面的写法改成下面这样，会更易于维护：
```css
background: url(tr.png) top right,
            url(br.png) bottom right,
            url(bl.png) bottom left;
background-size: 2em 2em;
background-repeat: no-repeat;
```

css的列表扩散规则：**如果只为某个属性提供一个值，那么它就会扩散并应用到列表中的每一项。**

#### css变量的定义和使用

```css
ul{
  --current-color: purple;  /* -- 定义变量*/
}
ol{
  --current-color: grey;
}
li{
  background: var(--current-color);  /*var 使用变量*/
}
```
以上代码实现的是：无序列表的li颜色为purple，有序列表的li颜色为grey。

## 第二章-背景与边框

#### 实现半透明边框
border边框的颜色会继承背景的颜色，我们可以用`background-clip`来设置边框颜色填充方法，默认为`border-box`,意味着背景会被元素的border box（边框的外延框）裁切掉。设为`padding-box`后，浏览器就会用内边距的外延来把背景裁切掉。

```css
div {
  border: 10px solid hsla(0,0%,100%,.5);
  background: white;
  background-clip: padding-box;
}
```
如此，便可实现半透明边框。

#### 多重边框

方法一：`box-shadow`方案

box-shadow接受第四个参数（‘扩张半径’），通过指定正值或负值，可以让投影面积加大或缩小。一个正值的扩张半径加上两个为零的偏移量以及为零的模糊值，得到的‘投影’就像一道实现边框。

而且box-shadow支持逗号分隔语法，因此可以创建任意数量的投影。但是此方法只能模拟实线边框。

但是也有三点需要注意：

    1、box-shadow是层层叠加的，因此扩张半径也是层层叠加的。例如下面的示例，在第一个10px半径的边框外再加一个5px的边框需要设置扩张半径的值为15px（10px+5px）。

    2、投影的行为与边框不完全一致，因为它不影响布局，而且也不会受到box-sizing布局的影响。不过可以通过内外边距的设置来模拟边框所需占据的空间。

    3、下面示例中的方法创建的假‘边框’出现在元素的外圈，它们并不会响应鼠标事件。可以通过给`box-shadow`属性加上inset关键字。不过需要为此增加额外的内边距来腾出假边框的空间。

```css
div {
	width: 100px;
	height: 60px;
	margin: 25px;
	background: yellowgreen;
	box-shadow: 0 0 0 10px #655,
              0 0 0 15px deeppink,
              0 2px 5px 15px rgba(0,0,0,.6);
}
```

方法二：outline方案

实线双重边框，先用常规border实线第一层，第二层使用outline（描边）。

```css
  background: yellowgreen;
  border: 10px solid #655;
  outline: 5px solid deeppink;  /*实线普通的双层边框*/
```
```css
  background: #333;
  border-radius: 10px;
  outline: 1px dashed #fff;
  outline-offset: -15px; /*使用负的outline-offset实线缝边效果*/
```

几点需要注意的：

    1、只能实线双层边框
    2、此方法实线的边框不会贴合border-radius实线的圆角效果。（具体效果可看code里的示例代码）

#### 灵活的背景定位

* background-position的扩展语法方案

background-position允许指定背景图片距离任意角的偏移量，只需要在偏移量前面指定关键字。

```css
div {
	background: url(http://csssecrets.io/images/code-pirate.svg)
	            no-repeat bottom right #58a; /*提供回退机制*/
	background-position: right 20px bottom 10px;
```

* background-origin方案

如果使用上述方法，当背景图片的偏移量与容器的内边距一致时，代码会是这样的：

```css
padding: 10px;
background: url(http://csssecrets.io/images/code-pirate.svg) no-repeat #58a;
background-position: right 10px bottom 10px;
```

这显然不够简洁，这个时候就可以使用background-origin来做得更加简洁：

```css
padding: 10px;
background: url(http://csssecrets.io/images/code-pirate.svg) no-repeat #58a
            bottom right; /*或者100% 100%*/
background-origin: content-box;
```

默认情况下，background-position的是以padding box为准的，这样边框才不会遮住图片。因此在`background-position: top left`这样的代码中`top left`是相对于padding box的（盒模型从内向外为： content box，padding box，border box）。

background-origin可以改变background-position的默认值，`background-origin: content-box`,这样，background-position中使用的边角关键字就会以内容区的边缘作为基准来。

在实际开发中，可以把background-position和background-origin结合起来使用。

* calc()方案

把背景图片定位到距离底边10px且距离右边20px的位置。如果以左上角偏移的思路来考虑，即希望它有一个100%-20px的水平偏移量，以及100%-10px的垂直偏移量，可以calc()来实现。

```css
background: url(http://csssecrets.io/images/code-pirate.svg)
            no-repeat bottom right #58a;
background-position: calc(100% - 20px) calc(100% - 10px); /*注意：calc()函数内部的 - 和 + 要加空白符，否则会报错。*/
```
