#### 云函数公用模块

自`HBuilderX 2.6.6-alpha`起，uniCloud提供了云函数模块公用方案。以下面的目录结构为例，介绍一下如何使用。

```
|--cloudfunctions
  |--common // 云函数公用模块目录
    |--hello-common // 云函数公用模块
      |--package.json
      |--index.js // 公用模块代码，可以不使用index.js，修改 package.json 内的 main 字段可以指定此文件名
  |--use-common // 使用公用模块的云函数
    |--package.json // 在 use-common 目录执行 npm init -y 生成
    |--index.js // 云函数入口文件
```

**创建并引入公用模块**

1. 在`cloudfunctions`目录下创建`common`目录
2. 在`common`目录右键创建公用模块目录（本例中为`hello-common`，见下方示例图），会自动创建入口`index.js`文件和`package.json`，**不要修改此package.json的name字段**
3. 在`hello-common`右键上传公用模块
4. 在要引入公用模块的云函数目录（本例中为`use-common`）执行`npm init -y`生成`package.json`文件
5. 在`use-common`目录执行`npm install ../common/hello-common`引入`hello-common`模块

![创建公用模块](https://img.cdn.aliyun.dcloud.net.cn/uni-app/uniCloud/create-common-module.jpg)

**注意事项**

- 如需修改公用模块需要在`common`目录下修改，修改之后不需要重新执行`npm install`。
- 如果要更新所有依赖某公用模块的云函数，可以在`common`目录下的公用模块目录（本例中为`hello-common`）右键选择`更新依赖本模块的云函数`
- 公用模块命名不可与nodejs内置模块重名

![更新公用模块](https://img.cdn.aliyun.dcloud.net.cn/uni-app/uniCloud/update-common-module.jpg)

**使用公用模块**

仍以上面的目录为例，在公用模块内`exports`，在云函数内`require`即可。示例代码如下：

```js
// common/hello-common/index.js
function getVersion() {
  return '0.0.1'
}
module.exports = {
  getVersion,
  secret: 'your secret'
}
```

```js
// use-common/index.js
'use strict';
const {
  secret,
  getVersion
} = require('hello-common')
exports.main = async (event, context) => {
  let version = getVersion()
  return {
    secret,
    version
  }
}
```

如果仅需要导出一个function还可以使用以下写法

```js
// common/hello-common/index.js
module.exports = function(e){
  return e
}
```

```js
// use-common/index.js
'use strict';
const echo = require('hello-common')
exports.main = async (event, context) => {
  let eventEcho = echo(event)
  return {
    eventEcho
  }
}
```
