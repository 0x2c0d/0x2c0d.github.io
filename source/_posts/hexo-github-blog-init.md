title: 用github hexo 搭建自己的blog
date: 2015-09-16 11:05:11
tags:
---
# 基本流程
* nodejs
  直接下载编译好的gz包，解压到某个文件夹，例如 /opt/nodejs
  然后在 ~/.profile 里面
  export NODEJS_HOME=/opt/nodejs
  export PATH=$PATH:$NODEJS_HOME/bin

  保存后，重启启动 terminal 就可以正常使用。
  
<!--more-->
* hexo
  npm install hexo --no-optional
  npm install -g hexo-cli
正常安装
   hexo init hexoblog
   cd  hexoblog
   npm install  把依赖都装上
   npm install hexo-deployer-git --save 把github的提交插件装上

* 配置发布到github
   修改 _config.yml 的deploy章节
    type: git
    repo: https://github.com/0x2c0d/0x2c0d.github.io.git

 回到 hexoblog 根目录
   hexo g
   hexo d
   输入github的用户名密码，然后就deploy成功了
   直接输入 0x2c0d.github.io 就可以访问了
* 源代码放到github
   上面的步骤只是把编译好的文件提交到了 0x2c0d.github.io这个工程上。
   而 hexoblog 没有保存到github上面
   所以在github上先新建一个 hexoblog的工程。
   git clone https://github.com/0x2c0d/hexoblog.git
   然后把原来创建建好的内容都复制到这个git工程里
   cp －rf hexoblog0/* hexoblog/
   然后进入 hexoblog 
   git add .
   git commit - m "init blog"
   git push  
 * 写文章
   cd hexoblog/source/_post/
   hexo new hexo-github-blog-int
   就会创建一个 hexo-github-blog-init.md文件
   然后可以用自己喜欢的编辑器进行编写
   写完之后，
   hexo g
   hexo s
   去 http://127.0.0.1:4000 看看效果
   可以的就 ctrl＋c关掉本地server
   然后 hexo d 提交到远程的 0x2c0d.github.io 上面。
   然后 回到 hexoblog目录
    git add .
    git commit -m "add new post"
    git push

   把最新的源代码也保存一下
   


 这样，就可以保持数据不丢失了。

