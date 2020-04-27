#### 1、JS

[javascript](https://javascript.info/)


#### 2、HTML元素属性

|   属性  | 描述  |
|  ----  | ----  |
|  id   | 规定元素的唯一  |
|  class   | 规定元素的类名  |
|  style  | 规定元素的行内样式  |
|  title  | 规定元素的额外信息（可在工具提示中显示）  |

#### 3、HTML常用标签

[Periodic Table of the HTML5 Elements](http://www.xuanfengge.com/funny/html5/element/)

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
