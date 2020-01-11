1. `<br />`推荐使用这种写法，为了更好的兼容性，`<hr />`同理
2. 文本格式化
```html5
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>菜鸟教程(runoob.com)</title>
	</head>
	<body>
		<h1>文本格式化：</h1>
			<b>加粗</b>
			<strong>加粗</strong>
			<i>斜体</i>
			<em>斜体</em>
			<small>这个文本是缩小的</small>
			<big>这个文本是放大的</big><br />
			这个文本包含
			<sub>下标</sub><br />
			这个文本包含
			<sup>上标</sup>
			
		<h1>预格式文本：</h1>
			<pre>
			此例演示如何使用 pre 标签
			对空行和    空格
			进行控制
			</pre>
			
		<h1>地址：</h1>
			<address>
			Written by <a href="mailto:webmaster@example.com">Jon Doe</a>.<br> 
			Visit us at:<br>
			Example.com<br>
			Box 564, Disneyland<br>
			USA
			</address>
			
		<h1>引用，会自动在引用周围加上双引号：</h1>
			<p>Here comes a short quotation: <q>This is a short quotation</q></p>
			<p>请注意，浏览器在引用的周围插入了引号。</p>
			
		<h1>删除以及强调效果：</h1>
			<p>My favorite color is <del>blue</del> <ins>red</ins>!</p>
	</body>
</html>
```
3. 链接
```html
<a href="https://www.runoob.com/" target="_blank">访问菜鸟教程!</a>
<p>如果你将 target 属性设置为 &quot;_blank&quot;, 链接将在新窗口打开。</p>
```
4. head元素的设置
```html
<!DOCTYPE html>
<html>
<head> 
	<!-- meta设置一些元数据 -->
	<meta charset="utf-8"> 
	<title>菜鸟教程(runoob.com)</title> 
	<base href="http://www.runoob.com/images/" target="_blank">
	<!-- link设置连接的资源文件 -->
	<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
	<body>
		<a href="base/">连接到base设置的默认链接</a>
	</body>
</html>
```