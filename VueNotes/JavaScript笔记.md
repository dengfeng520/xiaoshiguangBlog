#### 1、相关资料

[The Modern JavaScript Tutorial](https://javascript.info/)

[Periodic Table of the HTML5 Elements](http://www.xuanfengge.com/funny/html5/element/)

#### 2、HTML元素属性

|   属性  | 描述  |
|  ----  | ----  |
|  id   | 规定元素的唯一  |
|  class   | 规定元素的类名  |
|  style  | 规定元素的行内样式  |
|  title  | 规定元素的额外信息（可在工具提示中显示）  |

#### 3、HTML常用标签

* **块标签:容器元素，h1~h6,p**
* **行标签:span,a,img,video,audio**

##### (1)、文本相关

|   属性  | 描述  |
|  ----  | ----  |
|  标题 head   | <h1>h1 title</h1> <h2>h2 title</h2>  <h3>h3 title</h3> <h4>h4 title</h4> <h5>h5 title</h5> <h6>h6 title</h6> |
|段落 |<p>段落段落段落段落段落段落</p>|
| lorem   | 乱数假问，没有任何实际含义的文字  |
|  无语义，设置样式 | <span>Container with no semantic meaning.</span>  |
|<pre>pre</pre>|通常在网页中用于显示代码|
|<code>code</code>|显示代码|
|实体字符：通常在HTML中显示一些特殊字符 |&lt;P&gt;|


#### 4、HTML语义化
 * 每一个HTML标签元素都有具体的含义
 * 标签的显示效果是由CSS决定的
 * 因为浏览器带有默认的CSS样式，所有每个元素都有一些默认样式
 *  **选择什么元素取决于内容的含义而不是显示出的效果**
 *  块级元素：在显示时会独占一行， 行级元素：在显示时不会独占一行 （HTML 已经废止，被内容类别代替）
 *  实体字符：通常在HTML中显示一些特殊字符 

#### 5、HTML &lt;a&gt;

* 普通链接
* 锚链接

```
<a href="#chapter1">chapter1</a>
 
 <h2 id = "chapter1">
    chapter1
  </h2>
  <p>
    Lorem ipsum dolor sit amet consectetur adipisicing elit. Quos fugiat dolorum ipsa magnam suscipit similique, accusamus, sapiente commodi, voluptas repellendus ullam? Dolorum tempore error eveniet laudantium architecto placeat non fugit?
  </p>
```

* 发送邮件
* 点击后执行JS代码 javascript:

```
 <a href="mailto:deng_feng520@163.com">
      发送邮件
  </a>
```

* targer : 跳转窗口位置，默认是覆盖当前页面

  **_self: 覆盖掉当前页面**
  

 **_blank: 新的页面打开**


#### 6、HTML 容器元素

 容器元素：该元素代表一个块区域，内部用于放置其他元素。

* **div 元素 没有语义**

* **header 头部 标题 说明**
* **footer 尾部**
* **article 文章内容**
* **section 文章章节**
* **nav 导航菜单**
* **aside 附加信息，通常用于表示侧边栏**
* **del 错误的信息**
* **s 错误的信息**


#### 7、HTML 元素包含关系

* **元素的包含关系是由元素的内容类别决定的**
* **容器元素中可以包含任何元素**
* **a元素集合可以包含任何元素**
* **某些元素有固定的子元素(ul>li, ol>li, dl>dt+dd)**
* **标题元素和段落元素不能互相潜逃，并且不能包含容器元素**

#### 8、CSS 样式

|   属性  | 描述  |
|  ----  | ----  |
|  **color**  | **文字颜色**  |
|**background-color**|**背景颜色**|
|**px**|**像素：绝对单位，简单的理解为文字的高度占多少个像素**|
|**em**|**相对单位，相对于父元素的字体大小，每个元素必须有字体大小，如果没有申明，则直接适用父元素的的字体大小，如果没有父元素，则适用基准字号。**|
|**font-weight**|**文字粗细程度（）**|
|**text-decoration**|**下划线**|
|**text-indent**|**段落首行缩进**|
|**line-height**|**每行文本的高度，该值越大，每行文本的距离越大。设置行高为元素的高度，可以让单行文本垂直居中**|
|**letter-spacing**|**文字间隙**|

> <h3>选择器</h3>

<h4>(1)、常用选择器</h4>

* **ID选择器**
* **元素选择器**
* **类选择器**
* **通配符选择器**
* **属性选择器**

```
[href = "http://www.sina.com"] {
    color: red;
}
```
* **伪类选择器**

```
/* 鼠标悬停状态 */
a:hover {
    color: red;
}
/* 激活状态 鼠标按下状态*/
a:active {
    color: #008c8c;
}
/* 超链接未访问时的状态 */
a:link {
    color: black;
}
/* 超链接访问过后的状态 */
a:visited {
    color: crimson;
}
```
* **伪元素选择器**

```
span::before {
    content: "《";
    color: darkcyan;
}
span::after {
    content: "》";
    color: darkcyan;
}
```
<h4>(2)、选择器的组合</h4>

* 后代元素 空格
* 子元素 >
* 相邻兄弟元素 +
* 后面出现的所有兄弟元素 ～

#### 9、CSS 重叠

* 声明冲突： 同一个样式，多次应用到同一个元素
* 层叠: 解决声明冲突的过程，浏览器自动处理


<h4>(1)、比较重要性</h4>

* 作者样式表: 开发者书写的样式表 **!important**
* 作者样式表中的普通样式
* 浏览器默认样式表中的样式

<h4>(2)、比较特殊性</h4>

* 总体规则：选择器选中的范围越小越特殊 
* 具体规则：通过选择器计算出一个四位数字
  * 1、千位： 如果是内联样式 记1否则记0
  * 2、百位：等于所有选择器中所有id选择器的数量
  * 3、十位：等于选择中所有类选择器、属性选择器、伪类选择器
  * 4、个位：等于选择器中所有元素选择器、伪类选择的数量

<h4>(3)、比较源次序</h4>

 * 代码书写顺序靠后的胜出
 
<h4>(4)、应用</h4>

* 自定义的样式覆盖浏览器默认的样式

#### 10、CSS 继承

* 子元素会继承父元素的某些CSS属性
* 通常跟文字内容相关的属性都能被继承 

#### 11、CSS 页面渲染的过程

* **浏览器渲染页面是一个元素一个元素一次渲染的，顺序按照页面文档的树形结构目录进行**

![渲染](/Users/boxinkeji/Desktop/MyBlocks/xiaoshiguangBlog/VueNotes/RenderingOrder.png)

* **渲染每个元素的前提条件：该元素的所有CSS属性必须有值**

  * **（1）、确定申明值：**参考样式表中没有冲突的申明，作为CSS属性值
  * **（2）、层叠冲突：**对样式表中有冲突的申明适用层叠规则，确定CSS属性值
     * 比较重要性
     * 比较特殊性
     * 比较源次序
  * **（3）、使用继承：**对仍然没有值的属性，若可以继承，则继承父元素的值
  * **（3）、适用默认值:**对仍然没有值的属性，使用默认值

  ![vue003](/Users/boxinkeji/Desktop/MyBlocks/xiaoshiguangBlog/VueNotes/vue_003.png)
  
#### 11、CSS盒模型

**box:每个元素在页面中都会生成一个矩形区域**

* **盒子类型:** 此处区别于HTML中的行标签和块标签

  * 1、行盒: 在页面中不换行
  
   ```
   display: inline;
   ```
   
   * 2、块盒: 在页面中独占一行
   
   	```
   	display: block; 
   	```
   