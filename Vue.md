+ 当导入Vue.js文件之后，会产生一个全局变量Vue，实际是一个类。使用该类创建的对象被成为一个应用对象，或者Vue对象
```HTML
	<div id="app">
		{{ message }}
	</div>
	<script type="text/javascript">
	var app = new Vue({ 
		el: '#app',
		data: {
			message: 'Hello Vue!',
			name : "Vue"
		}
	});
	</script>
```
	- 使用{{ 变量 }}创建变量
	- 使用Vue创建对象的时候需要传递一个对象，该对象有两个属性，el代表元素对象，data代表数据对象，对应{{ 变量 }}中创建的变量

+ 每一个vue的应用都是通过vue函数创建一个新的Vue实例开始的
+ 当一个 Vue 实例被创建时，它将 data 对象中的所有的属性加入到 Vue 的响应式系统中。当这些属性的值发生改变时，视图将会产生“响应”，即匹配更新为新的值。
```JavaScript
// 我们的数据对象
var data = { a: 1 }

// 该对象被加入到一个 Vue 实例中
var vm = new Vue({
  data: data
})

// 获得这个实例上的属性
// 返回源数据中对应的字段
vm.a == data.a // => true     //此处的vim的a是Vue实例的data属性的data值得属性

// 设置属性也会影响到原始数据
vm.a = 2
data.a // => 2

// ……反之亦然
data.a = 3
vm.a // => 3
```
<font color="red">注意的是：使用的必须是数据对象中定义好的变量，若是不存在的变量，无论是使用Vue实例对象还是数据对象都无法起效，所以
对于要使用但一开始为空或者不存在，可以先设置一些初始值</font>
+ 使用Object.freeze(数据对象)可以将该数据对象无法追踪其变化
```JavaScript
	data = {message:"hello vue",name:"vue"}
	Object.freeze(data.name) // 可以精确到不能修改的属性如此出的name属性，message属性是可以修改的
	var app = new Vue({
		el: '#app',
		data: data,
	});
```
+ 除了数据属性（使用的是数据对象的属性），Vue 实例还暴露了一些有用的实例属性与方法。它们都有前缀 $，以便与用户定义的属性区分开来。
```JavaScript
var d = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: d
})

vm.$data === d // => true   使用的是vue对象的data属性
// 所以vm.$data.a 和 vm.a 和 d.a这三者是等价的
vm.$el === document.getElementById('example') // => true

// $watch 是一个实例方法
vm.$watch('a', function (newValue, oldValue) {
  // 这个回调将在 `vm.a` 改变后调用，newValue, oldValue分别对应新修改的值和原始的值
})
```
+ 实例生命周期钩子


+ 计算属性和方法的区别
	- 计算属性是基于响应式依赖来进行缓存的，只有响应式依赖发生改变时才会重新求值，而方法是只要重新渲染就会再次执行函数，而不用等待依赖
	- 使用计算属性比使用监听回调函数watch更好
	- 计算属性默认提供的是getter方法，即当相应关联的变量发生变化时本值就会发生变化
	- 而还可以给计算属性提供set方法，这个方法的功能，可以在修改本身时修改关联值
```JavaScript
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```
	- 计算属性和侦听watch的实现效果一致，主要的区别在于计算属性自身就是一个可以被调用的变量，而使用侦听，则需要创建一个新的变量用于接受相关值发生改变后操作的接受值。