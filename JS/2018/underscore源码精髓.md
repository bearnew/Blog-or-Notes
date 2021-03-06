## underscore源码精髓
### 1.void 0替代undefined
#### 优点：
* 避免undefined在局部作用域中被重写过
```js
(function() {
  var undefined = 10;

  // 10 -- chrome
  alert(undefined);
})()
```
* 节省字节大小
### 2.类型判断
* toString的this未定义，返回"[object Undefined]"
* toString的this定义返回"[object 参数的构造函数]"
```js
toString('123123') // [object undefined]

var x = {};
x.toString = toString;
x.toString('123123') // [object Object]

var y = [];
y[0] = toString;
y[0]('1231') // [object, Array]

```
```js
_.each(['Arguments', 'Function', 'String', 'Number', 'Date', 'RegExp', 'Error'], function(name) {
  _['is' + name] = function(obj) {
    return toString.call(obj) === '[object ' + name + ']';
  };
});
```
### 3.链式调用实现
```js
// 创建一个chain函数，用来支持链式调用
_.chain = function(obj) {
  var instance = _(obj);
  //是否使用链式操作
  instance._chain = true;
  return instance;
};
// 返回_.chain里是否调用的结果, 如果为true, 则返回一个被包装的Underscore对象, 否则返回对象本身
var chainResult = function(instance, obj) {
  return instance._chain ? _(obj).chain() : obj;
};

_.each(['concat', 'join', 'slice'], function(name) {
  var method = ArrayProto[name];
  _.prototype[name] = function() {
    //返回Array对象或者封装后的Array
    return chainResult(this, method.apply(this._wrapped, arguments));
  };
});
```
