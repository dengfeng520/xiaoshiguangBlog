---

title: CSS 盒模型

date: 2021-04-07 19:29:56

cover: /img/girl034.webp

---

在CSS中盒模型是非常重要的一个概念，可以说整个HTML界面的渲染都和盒模型息息相关，那么什么是盒子模型呢？一般在**网页中每个元素在页面中都会生成一个矩形区域**，这个区域就被称为盒子模型。我们通过CSS来控制这些盒子的大小、位置、以及其他属性。

### 1、盒子类型

* 行盒在页面中不换行
* 块盒在页面中独占一行

```html
<div>

</div>
```

##### 1.1、行盒

```css
div {
    display: inline;
}
```

##### 1.2、块盒

```CSS
div {
    display: block;
}
```

### 2、盒子的组成部分

盒模型主要由四个部分组成：

* 内容 content
* 填充 padding
* 边框 border
* 外边框 margin 

---

参考： 

[MSDN: Introduction to the CSS basic box model](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Box_Model/Introduction_to_the_CSS_box_model)

[w3school: CSS框模型](https://www.w3school.com.cn/css/css_boxmodel.asp)

[掘金：玩转CSS的艺术之美](https://juejin.cn/book/6850413616484040711)

