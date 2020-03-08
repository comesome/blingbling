<!-- ---
title: 开发环境准备
--- -->

# 开发环境准备

> 话说，工欲善其事，必先利其器

## 系统优化
### 基本设置
```bash
### F1-F12 功能键设置
System Preferences > Keyboard > Touch Bar shows

### Dock 放在左边（节约屏幕）
System Preferences > Dock > Position on screen

### 显示日期
System Preferences > Date & Time > Clock > Show date

### 光标加速 - 把 Key Repeat 和 Delay Until Repeat 拉到最右
System Preferences > Keyboard
```

### 优化 Homebrew
关闭 homebrew 自动更新  
vim ~/.bash_profile
export HOMEBREW_NO_AUTO_UPDATE=true   
`brew update` 需要的时候再手动更新homebrew


## Git 环境
Mac 有内嵌的 Git，但是在更新时不方便，所以需要用 homebrew 安装并更新 git

## 安装 Git 并升级至最新版
```bash
# 查看当前版本 git version 2.22.0
git version
# 查看 git 安装路径 /usr/local/bin/git
which git
# homebrew 安装
brew install git
# 将预设 Git 改为 homebrew 版本
brew link git --overwrite
# 以后 git 更新
brew upgrade git
# 检验  git version 2.25.0
git version
```

## SSH密钥配置
> 配置后不需要总输入密码

1. 生成密钥 -- 指定位置和名称
```bash
# 生成密钥
ssh-keygen -t rsa -C "xxxx@gmail.com" -f ~/.ssh/test_github
（遇到过 ssh 每过一段时间就失效的问题，用下面的步骤，一般用上面一个就够了）
# 确认ssh-agent处于启用状态 -- 输出 PID
eval "$(ssh-agent -s)"
# 将SSH key添加到ssh-agent
ssh-add ~/.ssh/test_github
# 测试连接
ssh -vT git@github.com
```

2. 复制公钥  
将test-github.pub文件的内容添加到github的“SSH KEYS”里面


## 配置用户名和密码
> 包含个人信息和别名配置，用户名邮箱在项目对应的配置下，别名在 `~/.bash_profile`  

在每个项目中单独配置的好处是，可以在不同的项目使用不同的邮箱，防止邮箱混乱导致 github 绿色小方块消失。  
```bash
# 进入项目中 .git 文件夹
cd {repo}/.git
git config user.name "EasterFan"
git config user.email "xxxx@gmail.com"
cat config
```

记录一下曾经用错邮箱丢失小绿砖又找回来的脚本(就是把原git历史中错误邮箱替换掉)：
```bash
#!/bin/sh
git filter-branch --env-filter '
OLD_EMAIL="旧的Email地址"
CORRECT_NAME="正确的用户名"
CORRECT_EMAIL="正确的邮件地址"
if [ "$GIT_COMMITTER_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_COMMITTER_NAME="$CORRECT_NAME"
    export GIT_COMMITTER_EMAIL="$CORRECT_EMAIL"
fi
if [ "$GIT_AUTHOR_EMAIL" = "$OLD_EMAIL" ]
then
    export GIT_AUTHOR_NAME="$CORRECT_NAME"
    export GIT_AUTHOR_EMAIL="$CORRECT_EMAIL"
fi
' --tag-name-filter cat -- --branches --tags
```

操作：  
```bash
# clone 到本地
cd repo

# 粘贴脚本，修改正确的用户名和邮箱
touch script.sh
chmod 777 script.sh && ./script.sh

# 正确历史 force 推送
git push --force --tags origin 'refs/heads/*'
```


## nodejs 环境
> 安装nvm-----配置nvm环境变量----安装node ----淘宝cnpm加速

### 安装 nvm
nvm是一个开源的 Node 版本管理器，通过简单的 bash 脚本来管理、切换多个 Node.js 版本,使用 nvm 可以安装官网最新版本之前的任意版本,可以任意切换不同版本  

下载：  
```bash
git clone https://github.com/creationix/nvm.git ~/.nvm && cd ~/.nvm && git checkout `git describe --abbrev=0 --tags`  
```
配置环境变量：  
```bash
# 打开文件
vim ~/.bashrc
# 加入位置
source ~/.nvm/nvm.sh
export NVM_DIR="/Users/YOURUSERNAME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"  # This loads nvm
# 检验
source  ~/.bashrc
nvm --version
```

### 安装 node
```bash
# 查找 node 版本
nvm ls-remote
# 安装node版本并设为默认node
nvm install 8.10.0
nvm alias default 8.10.0
# 检验
node --version
npm -v

# 查看已安装的node
nvm ls
# 切换 node 版本
nvm use 8.10.0
# 卸载node
nvm uninstall 8.10.0
```

### 淘宝 cnpm 加速
```bash
# 下载
npm install -g cnpm --registry=https://registry.npm.taobao.org
# 验证
cnpm -v
```
[淘宝npm镜像](http://npm.taobao.org/)



## ss 环境


### 命令行环境
#### 修改 bash 配置文件
原来的配置文件都写在 `.bash_profile` 里，但 iterm 依赖的 zsh 在登陆的时候默认只会加载 `~/.zshrc`,导致每次都要手动刷新配置文件，解决方法是编辑`~/.zshrc`：

```bash
# 打开 zshrc
vim ~/.zshrc

# 添加关联 bash_profile
export PATH=$HOME/bin:/usr/local/bin:$PATH
source $HOME/.bash_profile

# 刷新
source ~/.zshrc
```



#### 科学命令行
`brew cask install iterm2`

现在速度十几k：  
```bash
Cloning into 'taro'...
remote: Enumerating objects: 132, done.
remote: Counting objects: 100% (132/132), done.
remote: Compressing objects: 100% (96/96), done.
Receiving objects:  29% (24766/82792), 11.51 MiB | 17.00 KiB/s
```


## gitbook 环境

### 1. gitbook
```bash
# 先推送一版源码，再初始化创建分支并推动远程
git checkout -b gh-pages
git push origin gh-pages:gh-pages

# master + gh-pages 发布
bookgo
```
注bookgo - 在 `~/.bash_profile` 下的命令简写：  
```bash
# public gitbook to gh-pages in one tap -- by easter
alias bookgo="git add . && git commit -m 'Auot-update' && git pull --rebase && git push origin master && npm run docs && gh-pages -d website/build/blingbling"
```

### 2. atom 环境
#### 常用插件
表格功能： [markdown-table-editor](https://atom.io/packages/markdown-table-editor)  
![](https://raw.githubusercontent.com/wiki/susisu/atom-markdown-table-editor/images/demo.gif)    

粘贴图片改路径：Markdown-img-paste -- 在 settings 中设置图片路径(超级喜欢!)  

同步预览：markdown-scroll-sync
#### 常用快捷键

### 3. Github + Picgo 搭建个人图床

#### 配置 github
- 新建一个**公共仓库**
- 在公共仓库的`settings/developers settings`找到生成 token，也可直接访问 [https://github.com/settings/tokens/new](https://github.com/settings/tokens/new)

#### 配置 PicGo
### 4. 设置定时任务
> launchctl是一个统一的服务管理框架，可以启动、停止和管理守护进程、应用程序、进程和脚本等。
launchctl是通过配置文件来指定执行周期和任务的。

笔记时常更新，每次手动push就很麻烦，所以设置一个每天早上11点自动同步到GitHub的任务，Mac 上定时任务主要有两个命令：crontab 和 launchctl。这次尝试的是后者。  

#### 配置文件：
##### 在项目目录新建脚本`bookgo.sh`
- `echo $PATH`,先在命令行中执行，将输出结果添加到脚本第一行（因为 npm 配置在 Path 中，需要指定 PATH，否则无法找到 npm 命令）

- `chmod 777 bookgo.sh` 给脚本添加执行权限

```bash
#!/bin/sh
PATH=/Users/easterfan/.nvm/versions/node/v12.14.1/bin:/Users/easterfan/bin:/usr/local/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Applications/Postgres.app/Contents/Versions/latest/bin:/Users/easterfan/Software/redis-5.0.6/src
# 记录一下开始时间
echo `date` >> /Users/easterfan/Documents/bookgolog/log &&
# 进入bookgo.sh 所在目录
cd /Users/easterfan/Workspace/blingbling &&
# 执行命令
git add . && git commit -m 'Auot-update' && git pull origin master --rebase && git push origin master && npm run docs && gh-pages -d website/build/blingbling
# 运行完成
echo 'finish' >> /Users/easterfan/Documents/bookgolog/log
```

##### 新建 plist 定时任务
- 任务：每天早上11点执行blingbling/bookgo.sh,自动同步仓库
`cd ~/Library/LaunchAgents && touch com.easter.bookgo.plist`

```bash
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <!-- Label唯一的标识 -->
  <key>Label</key>
  <string>com.easter.bookgo.plist</string>
  <!-- 指定要运行的脚本 -->
  <key>ProgramArguments</key>
  <array>
    <string>/Users/easterfan/Workspace/blingbling/bookgo.sh</string>
  </array>
  <!-- 指定要运行的时间 -->
  <key>StartCalendarInterval</key>
  <dict>
        <key>Minute</key>
        <integer>00</integer>
        <key>Hour</key>
        <integer>11</integer>
  </dict>
<!-- 标准输出文件 -->
<key>StandardOutPath</key>
<string>/Users/easterfan/Documents/bookgolog/run.log</string>
<!-- 标准错误输出文件，错误日志 -->
<key>StandardErrorPath</key>
<string>/Users/easterfan/Documents/bookgolog/errrun.err</string>
</dict>
</plist>
```

支持两种方式配置执行时间：  

- StartInterval: 指定脚本每间隔多长时间（单位：秒）执行一次（具体的间隔）；

- StartCalendarInterval: 可以指定脚本在多少分钟、小时、天、星期几、月时间上执行（具体的时间点）


定时任务存放路径：  
plist 文件存放路径为/Library/LaunchAgents或/Library/LaunchDaemons，前者仅当用户登陆后才被执行，后者只要系统启动就会被执行。    
```bash
~/Library/LaunchAgents 由用户自己定义的任务项(建议)
/Library/LaunchAgents 由管理员为用户定义的任务项
/Library/LaunchDaemons 由管理员定义的守护进程任务项
/System/Library/LaunchAgents 由Mac OS X为用户定义的任务项
/System/Library/LaunchDaemons 由Mac OS X定义的守护进程任务项
```
##### 加载命令
`launchctl load -w com.easter.bookgo.plist`  
编写完 plist 文件后加载成功！

```bash
# 加载任务, -w选项会将plist文件中无效的key覆盖掉，建议加上
 launchctl load -w com.easter.bookgo.plist

# 删除任务
 launchctl unload -w com.easter.bookgo.plist

# 查看任务列表, 使用 grep '任务部分名字' 过滤
 launchctl list | grep 'com.easter'

# 立即执行一次任务
 launchctl start  com.easter.bookgo.plist

# 结束任务
 launchctl stop  com.easter.bookgo.plist
```
## 清理环境


## 快捷键管理
### Mac 自带快捷键

### idea 快捷键
常用快捷键  

| 快捷键  | 作用     |
| ------- | -------- |
| cmd + E | 最近修改 |
### atom 快捷键


## 参考
- [PicGo 文档](https://picgo.github.io/PicGo-Doc/zh/guide/)