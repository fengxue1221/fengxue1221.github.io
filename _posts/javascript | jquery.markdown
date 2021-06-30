### load() 方法

jQuery load() 方法从服务器加载数据，并把返回的数据放入到被选元素中。

```js
$(selector).load(URL,data,callback);
// URL 必须
// data 可选，与请求一同发送的查询字符串键/值对集合
// callback 可选，load()完成后回调的函数
// callback函数参数： 
// 1. responseTxt 包含调用成功时的结果内容
// 2. statusTxt 包含调用状态，success、error
// 3. xhr XMLHttpRequest对象
```