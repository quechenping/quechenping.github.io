---
title: Hexo、github搭建博客学习
date: 2021-08-01
tags: 学习
---
## Hexo简介
Hexo是一款基于Node.js的静态博客框架，可以方便的生成静态网页托管在GitHub和Coding上，是搭建博客的首选框架

### 1. 安装依赖
1. 先安装 git、node（不会的自行百度）
2. 安装hexo

``` bash
npm install -g hexo-cli
```
**hexo -v** 查看一下版本

### 2. 初始化hexo
进入任意文件夹下执行如下代码（代码存放位置文件夹）

``` bash
hexo init myblog
```
**myblog** 为文件夹名称

``` bash
cd myblog   //进入这个myblog文件夹
npm install
```

#### 新建完成后，指定文件夹目录下有：
- node_modules: 依赖包
- public: 存放生成的页面
- scaffolds: 生成文章的一些模板
- source: 用来存放你的文章,即编写该目录下的md文件
- themes: 主题
- _config.yml: 博客的配置文件

可以在根目录 **package.json** 下查看运行命令

#### 以下几种常用命令

| 命令      | 备注 |
| ----------- | ----------- |
| hexo clean  | 清除打包文件  |
| hexo generate   | 打包当前文件,类似yarn build|
| hexo server | 运行本地环境，基于generate的build文件 |
| hexo deploy | 发布文件到线上 |

执行顺序为 **hexo clean && hexo generate && hexo server && hexo deploy**
执行完 **hexo server** 即可打开 **localhost:4000** 查看博客

### 3. 连接hexo和github
##### 至此本地hexo项目已经跑起来了，只需要在github上引用本地代码即可

#### 1. 首先在github上注册账号，如有账号则直接登陆
1. 点击 **New repository** 创建一个新的仓库

2. 创建一个和你用户名相同的仓库，后面加 **.github.io**，只有这样，将来要部署到GitHub page的时候，才会被识别，也就是 **xxxx.github.io**

#### 2. 生成SSH添加到GitHub
1. 终端中输入

``` bash
git config --global user.name "yourname"
git config --global user.email "youremail"
```
2. 然后创建SSH,一路回车
``` bash
ssh-keygen -t rsa -C "youremail"
```
如下图
![createSSH](/images/createSSH.jpg)

ssh，简单来讲，就是一个秘钥，其中，**id_rsa**是你这台电脑的私人秘钥，不能给别人看的，**id_rsa.pub**是公共秘钥，可以随便给别人看。把这个公钥放在GitHub上，这样当你链接GitHub自己的账户时，它就会根据公钥匹配你的私钥，当能够相互匹配时，才能够顺利的通过git上传你的文件到GitHub上

而后在GitHub的 **setting** 中，找到 **SSH keys** 的设置选项，点击**New SSH key** ,把你的 **id_rsa.pub** 里面的信息复制进去。

打开终端，执行如下命令检查是否成功
``` bash
ssh -T git@github.com
```
### 4. 将hexo部署到GitHub
这一步，我们就可以将hexo和GitHub关联起来，也就是将hexo生成的文章部署到GitHub上，打开站点配置文件 **_config.yml**，翻到最后添加如下代码，xxx为github名称
``` bash
deploy:
  type: git
  repo: https://github.com/xxx/xxx.io.git
  branch: master
```
然后执行如下命令
``` bash
hexo clean
hexo generate
hexo deploy
```
得到如下结果则为成功,打开 **http://yourname.github.io** 即可看到你的博客嘞。

![deploy](/images/deploy.jpg "deploy")

### 5. hexo基础配置
#### 1. 配置文件_config.yml
在文件根目录下的 **_config.yml**，就是整个hexo框架的配置文件了。可以在里面修改大部分的配置。详细可参考[官方的配置](https://hexo.io/zh-cn/docs/configuration)描述。

#### 2. themes
可以在[官网](https://hexo.io/themes/)中挑选心仪的主题进行下载，放在自己源代码的themes文件夹下，然后在 **_config.yml**下将themes配置改为自己下载的主题名称

#### 3. 新加一篇文章
``` bash
hexo new page board
```
系统会自动给你在source文件夹下创建一个board文件夹，以及board文件夹中的 index.md，这样你访问的board对应的链接就是http://xxx.xxx/board

#### 4. 保存草稿 draft

draft是草稿的意思，也就是你如果想写文章，又不希望被看到，那么可以执行如下
``` bash
hexo new draft newpage
```
这样会在source/_draft中新建一个newpage.md文件，如果你的草稿文件写的过程中，想要预览一下，那么可以使用

``` bash
hexo server --draft
```
在本地端口中开启服务预览，如果你的草稿文件写完了，想要发表到线上时

``` bash
hexo publish draft newpage
```

### 6. git分支进行多终端工作
不能把文章只放在一台电脑上保存，这样换设备就无法更新博客

因此把hexo项目放git上进行管理

#### hexo机制

**hexo deploy** 上传部署到github的其实是hexo编译后的文件，是用来生成网页的，不包含源文件（git只存了public文件夹下的文件），所以我们需要把开发时需要的所有文件都存在git上

1. 首先在git的同名项目下创建一个新分支，假设命名为hexo

2. 然后在这个仓库的settings中，选择默认分支为hexo分支（这样每次同步的时候就不用指定分支，比较方便）

3. 然后将代码clone到本地，把除了.git 文件夹外的所有文件都删掉

4. 把之前我们写的博客源文件全部复制过来，除了.deploy_git。这里应该说一句，复制过来的源文件应该有一个.gitignore，用来忽略一些不需要的文件，如果没有的话，自己新建一个，在里面写上如下，表示这些类型文件不需要git:

``` bash
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```

注意，如果你之前克隆过theme中的主题文件，那么应该把主题文件中的.git文件夹删掉，因为git不能嵌套上传，最好是显示隐藏文件，检查一下有没有，否则上传的时候会出错，导致你的主题文件无法上传，这样你的配置在别的电脑上就用不了了。

然后git提交三步走，此时即将代码放在git上管理
切换电脑后，按照本文档从头再走一遍安装的依赖，然后拉下git上的文档代码，即可编写

### 7. 疑难杂症
#### hexo中图片上传
在source中添加images文件夹存放图片文件
在_config.yaml 中设置

``` bash 
post_asset_folder: true
```
图片路径引用为 **/images/deploy.jpg** 即可

### 8. 参考文档
[hexo史上最全搭建教程](https://www.cnblogs.com/jscs/p/13582883.html)

[hexo官网](https://hexo.io/)

[markdown官网](https://markdown.com.cn/)
