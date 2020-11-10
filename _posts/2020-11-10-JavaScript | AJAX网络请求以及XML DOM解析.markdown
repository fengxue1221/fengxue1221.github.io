### ajax

AJAX = Asynchronous JavaScript and XML （异步的JavaScript和XML）

ajax是与服务器交换数据并更新部分网页，可以在不重新加载整个页面的情况下。

ajax是一种在无需重新加载整个页面的情况下，能够更新部分网页的技术。

ajax是一种用于创建快速动态网页的技术。

通过在后台与服务器进行少量数据交换，ajax可以使网页实现异步更新，这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

XMLHttpRequest是AJAX的基础。

#### XMLHttpRequest对象

所有现代浏览器均支持XMLHttpRequest对象（IE5和IE使用ActiveXObject）

XMLHttpRequest用于在后台与服务器交换数据。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

创建XMLHttpRequest对象的语法：

```js
variable = new XMLHttpRequest();
```

老版本的IE(IE5和IE6)使用ActiveX对象：

```js
variable = new ActiveXObject("Microsoft.XMLHTTP");
```

为了应对所有的现代浏览器，包括IE5和IE6，请检查浏览器是否支持XMLHttpRequest对象。如果支持，则创建XMLHttpRequest对象。如果不支持，则创建ActiveXObject:

```js
var xmlhttp;
if(window.XMLHttpRequest){
    //code for IE7+,Firefox ,Chrome, Opera, Safari
    xmlhttp = new XMLHttpRequest();
}else{
    //code for IE5,IE6
    xmlhttp = new ActiveXObject("Microsoft.XMLHTTP");
}
```

#### 向服务器发送请求

`XMLHttpRequest`对象用于和服务器交换数据。

如果将请求发送到服务器，我们使用XMLHttpRequest对象的open()和send()方法：

```js
xmlhttp.open("GET","test1.txt",true);
xmlhttp.send();
```

方法：open(method,url,async) 
规定请求的类型，URL以及是否异步处理请求。

参数：
- method: 请求的类型GET或POST
- url: 文件在服务器上的位置
- async: true（异步）或false（同步）

方法：send(string)
将请求发送到服务器

参数
- string：仅用于POST请求


##### GET还是POST？

与POST相比，GET更简单也更快，并且在大部分情况下都能用。

使用POST请求的情况：
- 无法使用缓存文件（更新服务器上的文件或数据库）
- 向服务器发送大量数据（POST没有数据量限制）
- POST更稳定，更可靠

方法：setRequestHeader(header,value)
向请求添加HTTP头。
参数
- header：规定头名称
- value：规定头的值

##### 异步

ajax指的是异步Javascript和xml。
XMLHttpRequest对象要用于ajax的话，其open()方法的async参数必须设置为true。

```js
xmlhttp.open("GET","ajax_test.asp",true);
```

异步的好处是：
- 在等待服务器响应时可以执行其他脚本。
- 在响应就绪后可以对响应进行处理。

async=true时，需要指定`onreadystatechange`函数
```js
xmlhttp.onreadystatechange = function(){
    if(xmlhttp.readyState == 4 && xmlhttp.status == 200){
        document.getElementById("myDiv").innerHTML = xmlhttp.responseText;
    }
}
xmlhttp.open("GET","test.txt",true);
xmlhttp.send();
```

不推荐使用async=false，但对于小型的请求，也是可以的。
当使用async=false时，不用`onreadystatechange`函数，而是直接把代码放到send()语句后面：
```js
xmlhttp.open("GET","test.txt",false);
xmlhttp.send();
document.getElementById("myDiv").innerHTML = xmlhttp.responseText;
```

##### 服务器响应
如需获得来自服务器的响应，使用XMLHttpRequest对象的responseText或responseXML属性。

- responseText:获得字符串形式的响应数据。
- responseXML:获得XML形式的响应数据。

XML对象需要进行解析：
```js
xmlDoc = xmlhttp.responseXML;
txt = "";
x = xmlDoc.getElementsByTagName("title");
for (i=0;i<x.length;i++){
    txt = txt + x[i].childNodes[0].nodeValue + "<br />";
}
```

book.xml
```xml
<?xml version="1.0" encoding="GBK"?>
<bookstore>
    <book category="children">
        <title lang="en">Harry Potter</title>
        <author>J K. Rowling</author>
        <year>2005</year>
        <price>29.99</price>
    </book>
    <book category="cooking">
        <title lang="en">Everyday Italian</title>
        <author>Giada De Laurentiis</author>
        <year>2005</year>
        <price>30.00</price>
    </book>
</bookstore>
<!--
    根节点：<bookstore>，文档中所有其他节点都包含在<bookstore>中
    两个<book>节点
    每个<book>节点中包含四个节点：<title>,<author>,<year>,<price>
    这四个节点里的文本是文本节点
-->
```

XML DOM定义了访问和处理XML文档的标准方法。（XML Document Object Model：XML文档对象模型）
- 用于XML的标准对象模型
- 用于XML的标准编程接口
- 中立于平台和语言
- W3C的标准

XML DOM 定义了所有XML元素的对象和属性，以及访问它们的方法（接口）。也就是说，XML DOM 用于获取、更改、添加或删除XML元素的标准。

DOM 这样规定：
- 整个文档是一个文档节点
- 每个XML标签是一个元素节点
- 包含在XML元素中的文本是文本节点
- 每一个XML属性是一个属性节点
- 注释属于注释节点

父节点：Parent Node
子节点：Children Node
同级节点：Sibling Node

Error: Access Across Domains
处于安全方面的原因，现代浏览器不允许跨域访问。也就是意味着，网页以及加载的XML文件，都必须位于相同的服务器上。

XML DOM 属性：x是一个节点对象。
- x.nodeName: x的名称
- x.nodaType: x的类型，如果节点类型是“1”，则是元素节点
- x.nodeValue: x的值
- x.parentNode: x的父节点
- x.childNodes: x的子节点
- x.attributes: x的属性节点

XML DOM 方法：x是一个节点对象
- x.getElementsByTagName(name) 获取带有指定标签名称的所有元素。
- x.appendChild(node) 向x插入子节点
- x.removeChild(node) 从x删除子节点

```js
txt = xmlDoc.getElementsByTagName("title")[0].childNodes[0].nodeValue
// xmlDoc 由解析器创建的XML DOM
// getElementsByTagName("title")[0] 第一个<title>元素
// childNodes[0] <title>元素的第一个子节点（文本节点）
// nodeValue 节点的值(文本本身)
```

getElementsByTagName(name)方法返回节点列表（node list），节点列表是节点的数组。

注意：
```js
x.getElementsByTagName("title"); //返回x元素下所有<title>元素
xmlDoc.getElementsByTagName("title");//返回xml文档中所有的<title>元素
```

##### 节点的属性

在XML文档对象模型（DOM）中，每个节点都是一个对象。
- nodeName
  - nodeName是只读的
  - 元素节点的nodeName与标签名相同
  - 属性节点的nodeName是属性的名称
  - 文本检点的nodeName永远是#text
  - 文档节点的nodeName永远是#document
- nodeValue
  - 元素节点的nodeValue是undefined
  - 文本节点的nodeValue是文本自身
  - 属性节点的nodeValue是属性的值
- nodeType
  - 元素：1
  - 属性：2
  - 文本：3
  - 注释：8
  - 文档：9


##### onreadystatechange 事件
当请求被发送到服务器时，我们需要执行一些基于响应的任务。
每当readyState改变时，就会触发onreadystatechange事件。
readyState属性存有XMLHttpRequest的状态信息。

XMLHttpRequest属性：
- onreadystatechange: 回调函数，每当readyState改变时，就会调用该函数
- readyState：
  - 0:请求未初始化
  - 1:服务器连接已建立
  - 2:请求已接收
  - 3:请求已处理
  - 4:请求已完成，且响应已就绪
- status：
  - 200 “OK”
  - 404 未找到页面
