
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
