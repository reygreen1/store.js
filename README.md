store.js
========

store.js 提供了一套跨浏览器的本地存储解决方案。

```js
// 将 'marcus' 存为 'username'
store.set('username', 'marcus')

// 获取 'username'
store.get('username')

// 删除 'username'
store.remove('username')

// 清空所有索引
store.clear()

// 存储对象字面量 - store.js 内部使用了 JSON.stringify方法
store.set('user', { name: 'marcus', likes: 'javascript' })

// 获取存储的对象 - store.js 内部使用了 JSON.parse 方法
var user = store.get('user')
alert(user.name + ' likes ' + user.likes)

// 获取所有存储的值
store.getAll().user.name == 'marcus'

// 遍历所有存储的值
store.forEach(function(val, key) {
	console.log(key, '==', val)
})
```


工作机制
------------------
store.js 优先选择 localStorage 来进行存储，在 IE6 和 IE7 下降级使用userData来达到目的。 没有使用 flash ，不会减慢你的页面加载速度。也没有使用 cookies ，不会使你的网络请求变得臃肿。

store.js 依赖 JSON 来序列化数据进行存储。


安装
------------
只需获取 [store.min.js] 或者 [store+json2.min.js] ，并通过script标签引入即可使用。


`store.enabled` flag
--------------------
如果你的产品使用了 store.js，使用前你首先要检查 `store.enabled` 标识：

```html
<script src="store.min.js"></script>
<script>
	init()
	function init() {
		if (!store.enabled) {
			alert('Local storage is not supported by your browser. Please disabled "Private Mode", or upgrade to a modern browser')
			return
		}
		var user = store.get('user')
		// ... and so on ...
	}
</script>
```

在使用localStorage的时候，虽然可用但是会抛出一个错误。例如在使用 Safari的private browsing 模式（无痕浏览模式）的时候就会发生这样的情况。 其他浏览器允许用户临时禁止localStorage的使用。 Store.js 会检测这些设置并相应的更改 `store.enabled` 标识。


视频
-----------
[Introductory Screencast to Store.js](http://javascriptplayground.com/blog/2012/06/javascript-local-storage-store-js-tutorial) 由 Jack Franklin制作。



在node.js中使用
----------
只需设置下global.localStorage，store.js 在 node.js也可以正常使用，如下：

```
global.localStorage = require('localStorage')
var store = require('./store')
store.set('foo', 1)
console.log(store.get('foo'))
```


支持的浏览器
------------------
 - Tested in iOS 4
 - Tested in iOS 5
 - Tested in iOS 6
 - Tested in Firefox 3.5
 - Tested in Firefox 3.6
 - Tested in Firefox 4.0+
 - Support dropped for Firefox < 3.5 (注意下文提示)
 - Tested in Chrome 5
 - Tested in Chrome 6
 - Tested in Chrome 7
 - Tested in Chrome 8
 - Tested in Chrome 10
 - Tested in Chrome 11+
 - Tested in Safari 4
 - Tested in Safari 5
 - Tested in IE6
 - Tested in IE7
 - Tested in IE8
 - Tested in IE9
 - Tested in IE10
 - Tested in Opera 10
 - Tested in Opera 11
 - Tested in Opera 12
 - Tested in Node.js v0.10.4 (with https://github.com/coolaj86/node-localStorage 1.0.2)

*个人模式* Store.js 在浏览器开启了private mode模式（无痕浏览）的情况下可能会失效。 这就需要在使用  store.js前一定要检查 `store.enabled` 标识。

*Saucelabs.com 给力* 非常感谢Saucelabs.com对 store.js 进行了大范围的浏览器测试工作。可以去看看他们的网站，真的很棒。

*Firefox 3.0 & 2.0:* 高于v1.3.6版本的不再支持 FF 2 & 3 。 所以，如果你想要支持FF的古老版本，那你需要使用 v1.3.5 版本的 store.js。

*重要提示：* 在IE6和IE7中，如果你要存储键值对，那么很多特殊字符是不允许出现在关键字名称中的。  在[@mferretti](https://github.com/mferretti)，提供了一种合适的变通方法，就是把这些特殊字符替换为t "___"。


存储大小限制
--------------
 - IE6 和 IE7中: 总大小最多1MB ， 但是每个 "path" 或 "document" 最多128kb(参考 http://msdn.microsoft.com/en-us/library/ms531424(v=vs.85).aspx)。
 - 去这里 http://dev-test.nemikor.com/web-storage/support-test/ 可以查看不同浏览器的限制情况。

不支持的浏览器
-------------------
 - Firefox 1.0: 不支持 (除非是 cookies 和 flash)
 - Safari 2: 不支持 (除非是 cookies 和 flash)
 - Safari 3: 没有同步的 api (有异步的 sqlite api，但是 store.js 是同步的)
 - Opera 9: 不清楚是否有同步的api来进行本地存储 
 - Firefox 1.5: 不清楚是否有同步的api来进行本地存储 


序列化时的几点注意
---------------------------
如果不使用store.js，那么使用localStorage的时候，被存储的每个值上都调用了一次toString方法。这就是说，你不能方便的对numbers、 objects 或者 arrays进行存取：

```js
localStorage.myage = 24
localStorage.myage !== 24
localStorage.myage === '24'

localStorage.user = { name: 'marcus', likes: 'javascript' }
localStorage.user === "[object Object]"

localStorage.tags = ['javascript', 'localStorage', 'store.js']
localStorage.tags.length === 32
localStorage.tags === "javascript,localStorage,store.js"
```

我们想要的效果 (通过 store.js可以实现) 是

```js
store.set('myage', 24)
store.get('myage') === 24

store.set('user', { name: 'marcus', likes: 'javascript' })
alert("Hi my name is " + store.get('user').name + "!")

store.set('tags', ['javascript', 'localStorage', 'store.js'])
alert("We've got " + store.get('tags').length + " tags here")
```

JSON是JavaScript的原生序列化引擎。但是在使用store.js的时候，你不需要自己去对值进行序列化或反序列化，在每次调用 store.set() 和 store.get()的时候，store.js已经分别通过JSON.stringify() 和 JSON.parse() 实现了相同的效果。

一些浏览器并不支持原生的 JSON。所以，你需要同时引入 [JSON.js](本文件夹中包含的时未压缩的版本)。


没有 sessionStorage和auto-expiration?
----------------------------------
不必担心。我觉得没办法提供跨浏览器的 sessionStorage 。 然而，在 store.js上可以看到设置值的生存期的代码：

```js
var storeWithExpiration = {
	set: function(key, val, exp) {
		store.set(key, { val:val, exp:exp, time:new Date().getTime() })
	},
	get: function(key) {
		var info = store.get(key)
		if (!info) { return null }
		if (new Date().getTime() - info.time > info.exp) { return null }
		return info.val
	}
}
storeWithExpiration.set('foo', 'bar', 1000)
setTimeout(function() { console.log(storeWithExpiration.get('foo')) }, 500) // -> "bar"
setTimeout(function() { console.log(storeWithExpiration.get('foo')) }, 1500) // -> null
```


测试
-------
对于浏览器来说：可以到 http://marcuswestin.github.io/store.js/test.html 来测试最新版本的 store.js。

对于本地测试：可以 `npm install node-static && ./node_modules/node-static/bin/cli.js` 后，访问 http://localhost:8080 来测试。

(注意： test.html 只能通过 http:// 或 https://来访问。 这是因为通过 file://协议来访问时，localStore 会失效。)

对于Nodejs：通过 `npm install . localStorage && node test-node.js`


  [JSON.js]: http://www.json.org/json2.js
  [store.min.js]: https://raw.github.com/marcuswestin/store.js/master/store.min.js
  [store+json2.min.js]: https://raw.github.com/marcuswestin/store.js/master/store+json2.min.js
