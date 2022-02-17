---
title: hexo
author: helaine
date: 2022-02-13 15:48:59
tags: ['hexo']
categories: hexo注意事项
---

{% note info,  在github上找到了yuang01大佬写的[hexo主题](https://yuang01.gitee.io/)进行了我个人博客的搭建，主要是自己知识的总结，在这个过程中也遇到了不少问题，所以想记录一下，也算作我搭建博客的学习心得吧。 %}


# hexo搭建博客注意事项
谷歌了很多资料，发现可以用hexo进行静态搭建博客，我是用hexo把博客放在了我的github上面，网上有很多教程，再次不再多赘述，主要想列举一些在开发过程中遇到的问题。

{% title h2, 一些报错 %}
### **hexo部署文章到github出现443问题**
当时在部署的时候出现443问题，下面是代码报错实例：
```
fatal: unable to access 'https://github.com/xxx/xxx.github.io.git/': LibreSSL SSL_connect: SSL_ERROR_SYSCALL in connection to github.com:443 
FATAL {
  err: Error: Spawn failed
      at ChildProcess.<anonymous> (/Users/kevin/Desktop/xxx.github.io/node_modules/hexo-deployer-git/node_modules/hexo-util/lib/spawn.js:51:21)
      at ChildProcess.emit (events.js:315:20)
      at Process.ChildProcess._handle.onexit (internal/child_process.js:277:12) {
    code: 128
  }
} Something's wrong. Maybe you can find the solution here: %s https://hexo.io/docs/troubleshooting.html
```
解决办法：
* 对_config.yml文件进行修改把deploy的地址从http换成git
```
//eg : 以自己为例
repository: git@github.com:heelaine/heelaine.github.io.git
```

### **hexo部署出现TypeError: Cannot read property 'trim' of undefined**
这个时候多半是你写入markdown的内容有误，hexo并没有识别出你的内容。
解决办法：
* 检查文章，是不是出现格式错误或者未知字符，建议写page的时候不要写完再保存，写一段就保存一段，这样找错比较方便。

{% title h2, 格式化 %}
**最好不要格式化！！**
相信很多人在开发的时候，会在vscode上面设置自己的格式化规则，我们在下载主题进行配置的时候，也会理所当然直接自动保存格式化，当初我也是这么做的，但是每次一保存就会给我报红，感觉自己改的没有问题，一保存就出错真的很心累，后来才找到原因，因为我自己设置的格式化会改变它原来主题的代码格式导致hexo不能正常运行，我关了之后再去改代码就方便多了。

{% title h2, 图片路径 %}
```
$ npm install hexo-image-link --save
```
在编写个人博客的时候，通常会想要插入自己想要的图片，hexo的文章编写是通过markdown的格式来实现的，我们在这里就可以用markdown的格式来插入图片!(图片描述)[图片路径]，但是图片路径需要注意。
解决办法：
* 下载**hexo-image-link**包;
* 修改**_config.yml**中的**post_asset_folder: true**。资源文件管理功能打开后，Hexo将会在你每一次通过 hexo new [layout] <title> 命令创建新文章时自动创建一个同名文件夹。
* 通过markdown的方式引入图片，这里只需要引入相对路径即可。
```
![服务器和客户端传输过程](1.png) 
//第一个括号是图片描述
//第二个括号是同名文件夹的图片名字
```
一般情况下图片都能正常显示了，但是我发现我的图片还是裂开的并不能正常显示，我去检查我页面元素图片路径，发现路径有几段是重复的，这个时候只需要设置_config.yml的url就可以了
```
//eg : 以自己为例
url: https://heelaine.github.io/
```
这样操作下来就可以发现页面能正常显示图片，并且图片路径也是正确的了。

{% title h2, CDN图床 %}
因为个人博客可能会有很多需要图片的地方，但是如果一股脑都放在本地里，由于国内访问github总是时不时抽风且怕本地资源过于冗杂，所以我尝试通过外链的方式进行引用图片。
**图床**：就是专门提供存储图片的地方，有收费也有免费的，他会帮助我们进行图片的管理和优化，比如多机互备、CDN 加速、图片处理、图片鉴黄、文本识别等等。我使用的是[ipic](https://itunes.apple.com/cn/app/id1101244278?mt=12) 工具，免费版默认是微博图床。
**用法**（主要讲解的mac用户，window应该也差不多）：
1. 下载ipic工具并安装
2. 桌面顶部菜单就会出现ipic图标 
!()[ipic.jpg]
3. 点击图标选择markdown，把需要的图片拖拽到图标上
4. 这个时候图标上就会显示你需要上传的图片，单击图片就是复制图片链接

```
//eg: 以自己为例
![](https://tva1.sinaimg.cn/large/008i3skNgy1gz8uyzff92j30u0140gr7.jpg)
```

{% note warning, 可能有些人对CDN不是很了解，我也是在这次搭建过程中加深了我对cdn的理解，我们可以把cdn理解成快递点，当我们需要访问服务器上的资源的时候，有可能会遇到网站加载速度慢，资源出不来，高延迟等等问题，我们通常会通过cdn的方式进行加速，你想要访问什么资源就去最近的快递点去拿快递——最近的cdn节点获取资源，这样也能解决服务器的压力。具体的cdn的知识大家可以google一下～ %}

{% title h2,  jsDelivr + Github %}
如果用惯github的人，也可以通过{% pbg yellow, jsDelivr+github %}的方式。
**jsDelivr**：一个免费的cdn，支持npm，GitHub，wordpress等等。
解决办法：
1. 在github上创建一个仓库
2. 把图片等静态资源push到仓库里
3. 插入图片的地方需要修改链接
```
//eg : 以自己为例
![图片描述](https://cdn.jsdelivr.net/gh/用户名/仓库名/文件地址)
![图片描述](https://cdn.jsdelivr.net/gh/heelaine/PersonalSource/img/lxb/line5_2.jpg)
```
{% pbg yellow, 这种方式也能实现cdn对图片的加速 %}  


{% title h2,  生成page的模版 %}
在scaffolds文件夹中放置的是你生成new page的模版，默认生成的是post.md文件的模版，可以通过修改 **_config.yml**中的 **default_layout** 参数来指定默认布局。
```
//eg : 以自己的为例
---
title: {{ title }}
date: {{ date }}
author: helaine
tags:
categories:
---
```
```
//命令：默认生成post模版
hexo n  '文章名字'
```








