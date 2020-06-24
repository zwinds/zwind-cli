# 搭建一个自己cli
[中文]()     [English](https://github.com/zwinds/zwind-cli/README.en.md)
## 初始化项目
> npm init

执行完成之后会在根目录中生成一个package.json
![](https://tva1.sinaimg.cn/large/007S8ZIlly1gg2kof25g6j30nv0mydi0.jpg)

##安装自己的cli所需要的库

>npm i commander download-git-repo ora handlebars figlet clear chalk open watch inquirer

_执行之后会生成package-lock.json文件_

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gg2lbcbkuyj305y00ra9y.jpg)

其中：
[commander](https://www.npmjs.com/package/commander):设置一些node命令，如包的help、usage、version、parse输入的参数
[download-git-repo](https://www.npmjs.com/package/download-git-repo):下载git上的模板，并存到本地
[ora](https://www.npmjs.com/package/ora):下载包时，产生loading的图标
[handlebars](https://www.npmjs.com/package/handlebars):模板引擎
[figlet](https://www.npmjs.com/package/figlet):输出一些特殊的文字，这些文字只包含 ANSI 对应的字符
[clear](https://www.npmjs.com/package/clear):清除终端屏幕，相当于Ctrl + L
[chalk](https://www.npmjs.com/package/chalk):可以修改输出console颜色
[open](https://www.npmjs.com/package/open):打开在macOS，startWindows和xdg-open其他平台上使用命令
[watch](https://www.npmjs.com/package/watch):自动监听文件变化
[inquirer](https://www.npmjs.com/package/inquirer):用户判断，是否执行
* [x]  也可以根据自己的cli安装自己需要的库

## Cli搭建
* 新建bin目录

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gg2lg2pakrj307s03qq32.jpg)

* 添加执行的js文件，注意这里的js文件需要以#!/usr/bin/env node这个开头，表明用node执行
![](https://tva1.sinaimg.cn/large/007S8ZIlly1gg2lkhym6bj30i3058mxl.jpg)

* 在package.json中新建bin对象,这里存放之后需要执行的js文件

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gg2lw32dhqj30lv0awdh9.jpg)

* 将包npm link到全局
 
> npm link

_由于我这里的权限不足报错，我使用了sudo npm link_
![](https://tva1.sinaimg.cn/large/007S8ZIlly1gg2lwsi07yj30gu04bjry.jpg)

* 这时候再执行zwind命令，会发现执行了zwind.js脚本，这就相当于做了一个最简单cli工具


![](https://tva1.sinaimg.cn/large/007S8ZIlly1gg2m2o3ndzj30an01lmxa.jpg)

* 补全想要的功能

```js
#! /usr/bin/env node

const program = require('commander')
program.version(require('../package.json').version)

program 
    .command('init <name>')
    .description('init project ')
    .action(name =>{
        console.log('🥳完成添加' + name)
    })

program.parse(process.argv)
```

提示功能,版本信息就这样完成了

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gg2mmmbcpbj30dp093t9p.jpg)

* 接下来新建lib文件夹，并添加init.js

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gg2mp3d2wzj307p066aac.jpg)

```js
#! /usr/bin/env node
const {promisify} = require ('util')
const figlet = promisify(require('figlet'))

const clear = require('clear')
const chalk = require('chalk')
const log = content => console.log(chalk.green(content))
const {clone} = require('./download')
module.exports = async name =>{
    clear()
    const data = await figlet ('zwind Welcome')
    log(data)

    //clone
    log(`🛴创建项目 ${name}`)
    await clone(`github:zwinds/vue-template`, name)
}
```




* 新建download.js


![](https://tva1.sinaimg.cn/large/007S8ZIlly1gg2ntd5dwnj307p07fwev.jpg)
```js
const  {promisify} = require('util')
module.exports.clone = async function(repo,desc){
    const download = promisify(require('download-git-repo'))
    const ora = require('ora')
    const process = ora(`正在下载....${repo}`)
    process.start()
    await download(repo,desc)
    process.succeed()
}
```

这样就实现了下面的效果，能够从GitHub上下载模板

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gg2ntzb26yj30gz044mxd.jpg)

## 自动安装依赖
在init.js里添加

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gg2omgbz93j30u60k70x9.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gg2oo517lij30js0etdh0.jpg)

**完成效果**

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gg2op924doj30mq07874x.jpg)
![](https://tva1.sinaimg.cn/large/007S8ZIlly1gg2opw9vs2j30mq07b74q.jpg)

## 自动打开浏览器

```js
const open = require('open')
```

```js
    open(`http://localhost:8080`)
    //启动浏览器
    await spawn ('npm',['run','serve'],{cwd:`./${name}`})
```

 属于自己的cli已经基本完成
 
 -----
 
##发布到npm

* 首先需要注册npm账号

* 在根目录添加publish.sh
![](https://tva1.sinaimg.cn/large/007S8ZIlly1gg33mh1dw3j30vf0emq5k.jpg)
```js
#!/usr/bin/env bash
set -e
# 修改npm源地址
npm config get registry
npm config set registry=http://registry.npmjs.org
# 登陆输入自己的npm账号和密码，还有邮箱
echo '登录'
npm login
echo "发布中..."
npm publish
# 改回npm源地址
npm config set registry=https://registry.npm.taobao.org
echo -e "\n发布成功\n"
exit
```
-----
* 第一次发布版本

``npm adduser``


*依次输入账号，密码和邮箱*



* 不是首次发布

``npm login``

* 发布到npm上
 
 
``npm publish``
 
 
*依次输入账号，密码和邮箱*
##npm发布中遇到的错误信息

参考https://www.jianshu.com/p/7bba18925fbf

###1.使用npm config get registry 查看自己的镜像源是哪个
镜像源要设置为npm官方的   npm config set registry=http://registry.npmjs.org

```js
npm ERR! code E403
npm ERR! 403 403 Forbidden - PUT https://registry.npm.taobao.org/zwind-cli - 
[no_perms] Private mode enable, only admin can publish this module
npm ERR! 403 In most cases, you or one of your dependencies are requesting
npm ERR! 403 a package version that is forbidden by your security policy.

npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/zwind/.npm/_logs/2020-06-24T00_22_26_729Z-debug.log
```
###2.确保登录的用户账号正确，再次使用npm login/npm adduser登录
```js
npm ERR! code E404
npm ERR! 404 Not Found - PUT http://registry.npmjs.org/zwind-cli - Not found
npm ERR! 404 
npm ERR! 404  'zwind-cli@1.0.0' is not in the npm registry.
npm ERR! 404 You should bug the author to publish it (or use the name yourself!)
npm ERR! 404 
npm ERR! 404 Note that you can also install from a
npm ERR! 404 tarball, folder, http url, or git url.

npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/zwind/.npm/_logs/2020-06-24T00_33_28_324Z-debug.log

```

本项目Github地址：https://github.com/zwinds/zwind-cli







