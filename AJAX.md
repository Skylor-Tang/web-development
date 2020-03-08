+ 全名：AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。
+ AJAX依赖`XMLHttpRequest`对象，用于在后天和服务器交换数据，所有现代浏览器均支持 XMLHttpRequest 对象（IE5 和 IE6 使用 ActiveXObject）。
+ 语法：
	- `variable=new XMLHttpRequest();`
	- 老版本IE5和IE6使用ActiveX,`variable=new ActiveXObject("Microsoft.XMLHTTP");`
	- 为了保证兼容性，一般使用的时候都要做检测：
```JavaScript
	var xmlhttp;
	if (window.XMLHttpRequest)
	{
		//  IE7+, Firefox, Chrome, Opera, Safari 浏览器执行代码
		xmlhttp=new XMLHttpRequest();
	}
	else
	{
		// IE6, IE5 浏览器执行代码
		xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
	}
```
+ 使用XMLHttpRequest对象用于和服务器交换数据，需要使用XMLHttpRequest对象的open()和send()方法
	- open(method,url,async):method为请求的类型，GET或者POST，url为文件在服务器上的位置，async执行同步false还是异步true
	- send(string)：将请求放到服务器，但是只限在使用POST请求时才需要使用string参数，发送GET请求时，不需要传递string参数

+ 使用异步时，需要设置处于onreadystatechange事件中的就绪状态时执行的函数
+ 当请求被发送到服务器时，我们需要执行一些基于响应的任务。每当 readyState 改变时，就会触发 onreadystatechange 事件。readyState 属性存有 XMLHttpRequest 的状态信息。
```JavaScript
	xmlhttp.onreadystatechange=function()
	{
		// 添加条件判断，在 onreadystatechange 事件中，我们规定当服务器响应已做好被处理的准备时所执行的任务。当 readyState 等于 4 且状态为 200 时，表示响应已就绪：
		if (xmlhttp.readyState==4 && xmlhttp.status==200)
		{
			document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
		}
	}
```
onreadystatechange	存储函数（或函数名），每当 readyState 属性改变时，就会调用该函数。<font color="red">注意onreadystatuschange事件的位置，一定要在open()方法之前，因为状态中有0标志请求未初始化这样的在open函数之前的状态</font>
readyState	
	存有 XMLHttpRequest 的状态。从 0 到 4 发生变化。
	0: 请求未初始化
	1: 服务器连接已建立
	2: 请求已接收
	3: 请求处理中
	4: 请求已完成，且响应已就绪
status	200: "OK"
		404: 未找到页面
onreadystatechange 事件被触发 4 次（0 - 4）, 分别是： 0-1、1-2、2-3、3-4，对应着 readyState 的每个变化。每次的状态变化都会触发onreadystatuechange事件设置的函数。

+ 关于返回值的处理，AJAX的服务器响应有两种形式
	- responseText：以字符串的形式传递
	- responseXML：以xml的形式返回
	- ajax只负责了值得传递，值的处理我们可以是接受服务器返回的数据，在JavaScript中自己处理，或者，是在服务器端文件中负责处理，由服务端根据接受到的不同的ajax请求函数open传递的值决定。
