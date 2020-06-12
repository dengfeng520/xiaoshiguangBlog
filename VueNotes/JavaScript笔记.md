<h1><center>JavaScript学习笔记</center></h1>

<h6 align='right'>小时光</h6>
<h6 align='right'>北京体适能体育科技有限公司</h6> 


#### 1、相关资料

* <h3>JavaScript: J和S大写，其他字母小写<h3/>
* <h3>HTML: Hyper Text Markup Language四个字母全部大写<h3>
* <h3>CSS: Cascading Style Sheets 三个字母全部大写<h3/>

**[The Modern JavaScript Tutorial](https://javascript.info/)**

**[Periodic Table of the HTML5 Elements](http://www.xuanfengge.com/funny/html5/element/)**

**[JavaScript高级程序设计（第3版）](https://book.douban.com/subject/10546125/)**

**[JavaScript权威指南(第6版)](https://book.douban.com/subject/10549733/)**

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

##### (2)、行级元素和块级元素

**行级元素:大多为描述性标记**<br>
&ensp;&ensp;&ensp;&ensp;&ensp;1、和其他元素在同一行；<br> 
&ensp;&ensp;&ensp;&ensp;&ensp;2、高度、宽度以及内边距是不可控的；<br>
&ensp;&ensp;&ensp;&ensp;&ensp;3、宽高大小根据内容多少来填充，不可以改变;<br>
&ensp;&ensp;&ensp;&ensp;&ensp;4、不能包含块级元素；<br>
**块级元素:大多为结构性标记**<br>
&ensp;&ensp;&ensp;&ensp;&ensp;1、总是从新的一行开始； <br>
&ensp;&ensp;&ensp;&ensp;&ensp;2、宽、高大小是可控的；<br>
&ensp;&ensp;&ensp;&ensp;&ensp;3、宽度没有设置时，默认为100%；<br> 
&ensp;&ensp;&ensp;&ensp;&ensp;4、块级元素内部可以包含块级元素和行级元素；  

##### (3)、

| 行级元素统计  | 块级元素统计 | 
| :---------: | :--------: |
| `<span></span> `| `<address> </address>`| 
|`<a></a>`|`<h1></h1>`|
|`<br>`|`<h2></h2>`|
|`<b></b>`|`<h3></h3>`|
|`<strong></strong>`|`<h4></h4>`|
|`<img>`|`<h5></h5>`|
|`<sub></sub>`|`<h6></h6>`|
|`<sup></sup>`|`<hr>`|
|`<i></i>`|`<p></p>`|
|`<em></em>`|`<pre></pre>`|
|`<del></del>`|`<blockquote></blockquote>`|
|`<u></u>`|`<ul></ul>`|
|`<input>`|`<ol></ol>`|
|`<textarea></textarea>`|`<table></table>`|
|`<select></select>`|` <form></form>`|
||`<div></div>`|
||`<button></button>`|


##### (3)、严格模式

&ensp;&ensp;&ensp;&ensp;&ensp;在`IDE`中输入以下`JS`代码:

``` 
  number = 5;

  document.write("number=========" + number);
  
  number2 = null;
    
  document.write("number=========" + number2);
    
```
运行代码，
![Log](https://upload-images.jianshu.io/upload_images/1214383-9905ea01573bb424.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

&ensp;&ensp;&ensp;&ensp;&ensp;在	`JavaScript`文件中加入`"use strict";`
再次运行，发现报错`Uncaught ReferenceError: number is not defined`,
![Error](https://upload-images.jianshu.io/upload_images/1214383-8825ff69c734c448.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
修改代码：

```
  let number = 5;
  document.write("number=========" + number);
  let number2 = null;
  document.write("number=========" + number2);
```


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

```JavaScript
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

  ![RenderingOrder.png](https://upload-images.jianshu.io/upload_images/1214383-446ef43d68ab016e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


* **渲染每个元素的前提条件：该元素的所有CSS属性必须有值**

  * **（1）、确定申明值：**参考样式表中没有冲突的申明，作为CSS属性值
  * **（2）、层叠冲突：**对样式表中有冲突的申明适用层叠规则，确定CSS属性值
     * 比较重要性
     * 比较特殊性
     * 比较源次序
  * **（3）、使用继承：**对仍然没有值的属性，若可以继承，则继承父元素的值
  * **（3）、适用默认值:**对仍然没有值的属性，使用默认值
  

  ![vue_003.png](https://upload-images.jianshu.io/upload_images/1214383-37dbe4c92bfd5ec0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
#### 11、CSS盒模型

**box:每个元素在页面中都会生成一个矩形区域**

* **盒子类型:** 此处区别于HTML中的行标签和块标签

  * 1、行内元素(inline)：可以多个标签存在一行，对宽高属性值不生效，完全靠内容撑开宽高。
  
    ```JavaScript
    display: inline;
    ```
    
   * 2、块级元素(block)：独占一行，对宽高的属性值生效；如果不给宽度，块级元素就默认为浏览器的宽度，即就是100%宽。
   
       ```
       display: block; 
       ```
   
   * 3、行内块元素(inline-block)：结合的行内和块级的优点，既可以设置长宽，可以让padding和margin生效，又可以和其他行内元素并排。
   
   	```
   	display: inline-block;
   	```
  
      
* **盒子的组成部分:** 无论是行盒还是块盒，都由以下几个部分组成，从内到外分别是：

  * 1、内容 content
  
    *  width、height设置的是盒子内容的宽高
  
      ```JavaScript
       width: auto;
       height: 100%;
      ```
  
    *  部分内容通常叫做整个盒子的**内容盒 content-box**
  
  * 2、填充（内边距） padding 
  
    * 盒子边框到黑子内容的距离
  
      ```JavaScript
      padding-left: 0px;
      padding-right: 0px;
      ```
  
  * 3、边框 border
  
  * 4、外边框 margin
  
* **盒模型应用:** 

   * 1、改变宽高范围
     
     默认情况下，width 和 height 设置的是内容盒宽高。
     
   * 2、改变背景范围覆盖
     
     默认情况，背景覆盖边框盒
     可通过background-clip进行修改
   
   * 3、溢出处理

     overflow: 控制内容已出边框盒后的处理方式
     
     ```
    <p class = "overflowClassTxt">
    Lorem ipsum, dolor sit amet consectetur adipisicing elit. Quis facere, exercitationem officiis officia quibusdam ipsam impedit beatae mollitia ipsa ipsum illum totam ab non earum animi nihil iste minus quisquam!
</p>


     ```
     
     ```
  .overflowClassTxt {
       width: auto;
       height: 50px;
       font-size: 15px;
       background-color: #008c8c;
       border-color: red;
       border-style: solid; 
       border-width: 5px;
}
     ```
    ![vue_005.png](https://upload-images.jianshu.io/upload_images/1214383-4b3a0c1d7442cb06.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    
    加上一行代码：
    
    ```
    overflow-y: scroll;
    ```
    
   ![vue_006.gif](https://upload-images.jianshu.io/upload_images/1214383-dcb2dbdc9ca00fe5.gif?imageMogr2/auto-orient/strip)
        
   * 4、断词规则
    
   * 5、空白处理
   
* **行盒:**  
  
   * 1、盒子沿着内通延伸
     
   * 2、行盒不能设置宽高
   
      调整行盒的宽高，应该使用字体大戏、行高、字体类型来间接调整
   
   * 3、内边距（填充区）
     
     水平方向有效，垂直方向不会实际占据空间
     
   * 4、边框
   
     水平方向有效，垂直方向不会实际占据空间
     
   * 5、外边距
     
     水平方向有效，垂直方向不会实际占据空间
     
     
     设置行盒宽度和高度:
     
     ```
     <a> Google </a>
     
     a {
    background-color: #008c8c;
    padding: 0 20px;
    }
     ```
     
     ![vue_007.png](https://upload-images.jianshu.io/upload_images/1214383-bf35ddb9d64dfca2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
     
     ```
     a {
    background-color: #008c8c;
    padding: 0 50px;
    font-size: 2em;
    color: white;
}
     ```
     ![vue_008.png](https://upload-images.jianshu.io/upload_images/1214383-f7e45bcb20cf992e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
     
 或者直接变为行块盒:
 
 ```
 display: inline-block;
 ```
 
  * 6、行块盒
    
    ```
    <div class="pager">
    <a href="" class="selected">1</a>
    <a href="">2</a>
    <a href="">3</a>
    <a href="">4</a>
    <a href="">5</a>
    <a href="">6</a>
    <a href="">7</a>
    <a href="">8</a>
    <a href="">9</a>
    <a href="">10</a>
    </div>
    ```
    
    ```
    .pager {
    margin-top: 10px;
    text-align: center;
}
.pager a {
    margin-top: 8px;
    border: 1px solid #e1e2e3;
    color: #3388ff;
    width: 34px;
    height: 34px;
    display: inline-block;
    line-height: 34px;
}
.pager a:hover {
    border-color: 1px solid #38f;
    background-color: #f2f8ff;
 }
.pager a.selected {
    border: none;
    color: black;
    background-color: initial;
}
    ```
     ![vue_009.gif](https://upload-images.jianshu.io/upload_images/1214383-a24869e8a9574dd7.gif?imageMogr2/auto-orient/strip)
    
  * 7、可替换元素和非可替换元素

    大部分元素，页面上显示的结果，取决于元素的内容，称为非可替换元素
    少部分元素，页面上显示的结果，取决于元素属性，称为可替换元素
    
    可替换元素: img、video、audio
    绝大部分可替换元素均为行盒。 
    可替换元素类似于行块盒，盒模型中所有尺寸都有效。
    
   ```
   <img class="grilsImg" src=""/>

   ```   
    
    ```
    .grilsImg {
    width: 100%;
    height: auto;
    object-fit: contain;
    margin-top: 10px;
}
    ```
* **浮动盒子:**
      
    * 左浮动的盒子向上向左排列
    * 右浮动的盒子向上向右排列
    * 浮动盒子的顶部不得高于上一个盒子的顶部
    * 若剩余空间无法放下浮动的盒子，则该盒子向下移动，直到具备足够的空间能够容纳盒子，然后再向左或者向右移动
    * 浮动盒子的顶边不得高于上一个盒子的顶边

#### 12、盒子相关

* 盒子隐藏

```
display: none; 不生成盒子
visibility: hidden; 生成盒子，只是从视觉上移除盒子，盒子仍然占据空间
```

* 背景图

 ```
 background-image: url("");
```
  * 	默认情况下，图片会在横坐标和纵坐标重复
  
  ```
   background-position: 0px 0px; /* 从雪碧图中取出一部分 */
   background-attachment: fixed; /* 背景图不滚动，始终在最下层 */
  ```
* input
  
  ```
  type="search"
  type="text"
  type="date"
  type="checkbox"
  type="radio"
  type="number"
  ```
 
* 多个行盒垂直方向上的对齐
  
  ```
  vertical-align: middle;
  ```
  
* 图片的底部白边
  
  图片的父元素是一个块盒，块盒高度自动，图片底部和父元素底部之间往往会出现空白。
   
     * 1、设置父元素的字体大小为0
     * 2、将图片设置为块盒
     
        ```
         img {
           display: block;
         }
        ```
   
  
```
margin: style                  /*单值语法  所有边缘 */  举例： margin: 1em; 
margin: vertical horizontal    /*二值语法  纵向 横向 */  举例： margin: 5% auto; 
margin: top horizontal bottom  /*三值语法 上 横向 下*/  举例： margin: 1em auto 2em; 
margin: top right bottom left  /*四值语法 上 右 下 左*/  举例： margin: 2px 1em 0 auto; 

```