#Build your own cli

##Initialize project

> npm init


After execution, a package.json

![]( https://tva1.sinaimg.cn/large/007S8ZIlly1gg2kof25g6j30nv0mydi0.jpg )



##Libraries needed to install your own cli



>npm i commander download-git-repo ora handlebars figlet clear chalk open watch inquirer



A kind of Package will be generated after execution- lock.json Documents_


![]( https://tva1.sinaimg.cn/large/007S8ZIlly1gg2lbcbkuyj305y00ra9y.jpg )



Among them:

[commander](https://www.npmjs.com/package/commander）: set some node commands, such as help, usage, version, parse input parameters of the package

[download-git-repo]( https://www.npmjs.com/package/download-git-repo ）: download the template on GIT and store it locally

[ora]( https://www.npmjs.com/package/ora ）: when downloading a package, the loading icon is generated

[handlebars]( https://www.npmjs.com/package/handlebars ）: template engine

[figlet]( https://www.npmjs.com/package/figlet ）: output special text that contains only the characters corresponding to ANSI

[clear]( https://www.npmjs.com/package/clear ）: clear terminal screen, equivalent to Ctrl + L

[chalk]( https://www.npmjs.com/package/chalk ）: output console color can be modified

[open]( https://www.npmjs.com/package/open ）: open commands for use on MacOS, startwindows, and other XDG open platforms

[watch]( https://www.npmjs.com/package/watch ）: automatically listen for file changes

[inquirer]( https://www.npmjs.com/package/inquirer ）: judged by the user, whether to execute

*   [x] you can also install the library you need according to your cli



##CLI building

*   New bin directory



![]( https://tva1.sinaimg.cn/large/007S8ZIlly1gg2lg2pakrj307s03qq32.jpg )



*   Add the executed JS file. Note that the JS file here needs to start with ා! / usr / bin / env node, indicating that the node is used for execution

![]( https://tva1.sinaimg.cn/large/007S8ZIlly1gg2lkhym6bj30i3058mxl.jpg )



*   At package.json A new Bin object is created in. The JS file to be executed is stored here



![]( https://tva1.sinaimg.cn/large/007S8ZIlly1gg2lw32dhqj30lv0awdh9.jpg )



*   Link package NPM to global



> npm link



A kind of Because my permission here is insufficient, I used sudo NPM link_

![]( https://tva1.sinaimg.cn/large/007S8ZIlly1gg2lwsi07yj30gu04bjry.jpg )



*   At this time, execute the command zwind again, and you will find that it has been executed zwind.js Script, which is equivalent to making the simplest cli tool




![]( https://tva1.sinaimg.cn/large/007S8ZIlly1gg2m2o3ndzj30an01lmxa.jpg )



*   Complete the function you want



```js

#! /usr/bin/env node
const program = require('commander')
program.version (require('../ package.json ').version)
program
.command('init <name>')
.description('init project ')
.action(name =>{
console.log () 🥳 Finish adding '+ name)
}
program.parse ( process.argv )

```




Prompt function, version information is completed



![]( https://tva1.sinaimg.cn/large/007S8ZIlly1gg2mmmbcpbj30dp093t9p.jpg )



*Next, create a new lib folder and add init.js



![]( https://tva1.sinaimg.cn/large/007S8ZIlly1gg2mp3d2wzj307p066aac.jpg )



```js

#! /usr/bin/env node

const {promisify} = require ('util')
const figlet = promisify(require('figlet'))
const clear = require('clear')
const chalk = require('chalk')
const log = content => console.log ( chalk.green (content))
const {clone} = require('./download')
module.exports = async name =>{
clear()
const data = await figlet ('zwind Welcome')
log(data)


//clone

log(` 🛴 Create project ${name} `)
await clone(` github:zwinds/vue-template `, name)
}
```






*   New download.js




![]( https://tva1.sinaimg.cn/large/007S8ZIlly1gg2ntd5dwnj307p07fwev.jpg )

```js

const {promisify} = require('util')

module.exports.clone = async function(repo,desc){

const download = promisify(require('download-git-repo'))

const ora = require('ora')

Const process = ora (` downloading... ${repo} '))

process.start ()

await download(repo,desc)

process.succeed ()

}
```



In this way, the following effects are achieved, and templates can be downloaded from GitHub



![]( https://tva1.sinaimg.cn/large/007S8ZIlly1gg2ntzb26yj30gz044mxd.jpg )



##Auto install dependency

stay init.js Add in



![]( https://tva1.sinaimg.cn/large/007S8ZIlly1gg2omgbz93j30u60k70x9.jpg )



![]( https://tva1.sinaimg.cn/large/007S8ZIlly1gg2oo517lij30js0etdh0.jpg )



**Completion effect**



![]( https://tva1.sinaimg.cn/large/007S8ZIlly1gg2op924doj30mq07874x.jpg )

![]( https://tva1.sinaimg.cn/large/007S8ZIlly1gg2opw9vs2j30mq07b74q.jpg )


##Open browser automatically

```js

const open = require('open')

```



```js

open(` http://localhost :8080`)

//Launch browser

await spawn ('npm',['run','serve'],{cwd:`./${name}`})

```



The CLI of your own has been basically completed



-----



##Publish to NPM

*   First, you need to register an NPM account


*Add at root publish.sh

![]( https://tva1.sinaimg.cn/large/007S8ZIlly1gg33mh1dw3j30vf0emq5k.jpg )

```js

#!/usr/bin/env bash

set -e

#Modify NPM source address

npm config get registry

npm config set registry= http://registry.npmjs.org

#Log in and enter your NPM account, password and email

Echo 'Login'

npm login

Echo "Publishing..."

npm publish

#Change back to NPM source address

npm config set registry= https://registry.npm.taobao.org

Echo - e "\ npublish succeeded \ n"

exit

```

-----

*   First release



``npm adduser``

*Enter account number, password and email address in sequence*





*Not first release



``npm login``



*Publish to NPM




``npm publish``




*Enter account number, password and email address in sequence*

##Error messages encountered in NPM Publishing



reference resources https://www.jianshu.com/p/7bba18925fbf



###1. Use NPM config get registry to see which image source is your own

Image source should be set to NPM official NPM config set registry= http://registry.npmjs.org



```js

npm ERR! code E403

npm ERR! 403 403 Forbidden - PUT https://registry.npm.taobao.org/zwind-cli -

[no_ perms] Private mode enable, only admin can publish this module

npm ERR! 403 In most cases, you or one of your dependencies are requesting

npm ERR! 403 a package version that is forbidden by your security policy.



npm ERR! A complete log of this run can be found in:

npm ERR! /Users/zwind/.npm/_ logs/2020-06-24T00_ 22_ 26_ 729Z- debug.log

```

###2. Make sure the login user account is correct, and use NPM login / NPM addUser to login again

```js

npm ERR! code E404

npm ERR! 404 Not Found - PUT http://registry.npmjs.org/zwind-cli - Not found

npm ERR! 404

npm ERR! 404 ' zwind-cli@1.0.0 ' is not in the npm registry.

npm ERR! 404 You should bug the author to publish it (or use the name yourself!)

npm ERR! 404

npm ERR! 404 Note that you can also install from a

npm ERR! 404 tarball, folder, http url, or git url.

npm ERR! A complete log of this run can be found in:

npm ERR! /Users/zwind/.npm/_ logs/2020-06-24T00_ 33_ 28_ 324Z- debug.log



```



GitHub address of the project: https://github.com/zwinds/zwind-cli