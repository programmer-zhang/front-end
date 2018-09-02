> 在日常开发中经常会碰到需要改变数组或对象同时进行视图更新，vue的双向绑定本身也是支持这么做，不过我们在碰到新增属性时，可以改变属性，但Vue不会触发视图更新

## 使用 this.$set 进行属性的更新
* `Vue.set(vm.obj, 'e', value)` 或 `this.$set(vm.obj, 'e', value)`进行属性的添加，从而修改对象的同时进行试图更新
	* vm.obj 是要添加属性的对象
	* e 是添加的对象属性
	* value 是添加的对象属性的值

* [一个简单的小例子(vue实现倒计时)](https://github.com/programmer-zhang/com.frontend.www/blob/master/src/views/countDown.vue)