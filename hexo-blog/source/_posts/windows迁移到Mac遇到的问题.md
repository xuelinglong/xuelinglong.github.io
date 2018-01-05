---
title: windows迁移到Mac遇到的问题
date: 2017-10-29 22:04:19
tags: 搭建工作环境
---

# 安装hexo
```
$ npm install hexo
hexo: command not found
```
解决方案：安装Xcode --> 安装homebrew
```
$ npm install hexo -g
npm WARN checkPermissions Missing write access to /usr/local/lib/node_modules
npm ERR! path /usr/local/lib/node_modules
npm ERR! code EACCES
npm ERR! errno -13
npm ERR! syscall access
npm ERR! Error: EACCES: permission denied, access '/usr/local/lib/node_modules'
npm ERR!  { Error: EACCES: permission denied, access '/usr/local/lib/node_modules'
npm ERR!   stack: 'Error: EACCES: permission denied, access \'/usr/local/lib/node_modules\'',
npm ERR!   errno: -13,
npm ERR!   code: 'EACCES',
npm ERR!   syscall: 'access',
npm ERR!   path: '/usr/local/lib/node_modules' }
npm ERR!
npm ERR! Please try running this command again as root/Administrator.
npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/xxx/.npm/_logs/2017-10-27T01_21_01_871Z-debug.log
```
<!-- more -->
解决方案：
第一步,赋予目录权限:
``` 
$ sudo chown -R `whoami` /usr/local/lib/node_modules
```
第二步,安装hexo:
```
$ npm install hexo-cli -g 
```


# 配置ssh
```
$ ssh -T git@github.com 
ssh: connect to host port 22: Connection refused  
```
解决方案：
第一步,测试是否可以通过HTTPS端口SSH，请运行此SSH命令：
```
ssh -T -p 443 git@ssh.github.com         
嗨用户名  您已成功验证，但GitHub不提供shell访问。
```
第二步,添加~/.ssh/config文件并编辑：
```
Host github.com Hostname ssh.github.com Port 443 
```
 
# 文件配置转移
windows 下的博客根目录 hexo，复制该目录下的：_config.yml, scaffolds, source, themes；
mac 下的博客根目录 hexo，把刚才复制的内容，直接覆盖替换相同的文件文件夹。

```
$ hexo s 
{Error:Cannotfindmodule'./build/Release/DTraceProviderBindings'
......
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
...... 
```
解决方案：把windows 下的博客根目录 hexo下的node_modules拷贝到mac下替换。

如果在 hexo d 部署不成功，有可能是缺少了模块，安装以下再尝试：` npm install hexo-deployer-git --save `

# sourcetree
克隆非master分支的仓库代码:
第一步：克隆仓库；
第二步：查看分支；
第三步：切换分支；
第四步：获取最新的分支内容。
```
$ git clone https://github.com/xxx/xxx.git
$ cd xxx
$ git branch -a
$ git checkout -b 分支名 origin/分支名
$ git pull origin 分支名
```