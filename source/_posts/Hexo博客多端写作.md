---
title: Hexo博客多端写作
date: 2020-03-18 10:40:04
index_img: /cover/a0e9110685216418214660d713c041d4.jpg
banner_img: /cover/a0e9110685216418214660d713c041d4.jpg
tags:
	- hexo
---

### 前言

总所周知，Hexo没有一个总的博文管理后台，博客写作的前提是要拿到完整的hexo源代码，所以当我们处于另一台设备时，除非使用U盘拷贝随身携带，否则一旦换了设备就没办法写作了。

虽然可以借助U盘做到多端写作，但无疑这是一个很麻烦的过程。此时、我们会去想，有没有一个好用的 Hexo 多端同步方案来帮我们解决这些烦恼呢？答案是肯定的，我们可以使用github或者其他开源仓库进行版本控制实现多端写作

#### 创建Github仓库分支

  1. 修改Hexo博客目录下的`.gitignore`文件，在末尾加上

     ```
     /.deploy_git
     /public
     ```

     我是在idea编码，所以我还需要加一行

     ```
      .idea
     ```
     
  2. 执行git命令 创建分支
     ```
     git init  					# 初始化 git 仓库
     git remote add origin ${your github repository}
     # 添加 Github 远程仓库，其中：`[github repository]` 是你发布 Hexo 时所建立的仓库地址。比如：`git@github.com:d2gin/d2gin.github.io.git`
     git add . 					# 将变更添加到 git 暂存区
     git commit -m "${comment}"	# 提交本次更改，其中 ${comment} 是本次提交的说明，按照实际情况填写任意字符即可
     git push origin master:hexo # 将本地 master 分支的提交发布到远程仓库的 hexo 分支
     ```
     
     远程仓库中的 master 分支用于存储 hexo 生成的静态页面，hexo 分支用于存储 hexo 源文件；此时，我们已经成功将 Hexo 备份到了远程仓库的 hexo 分支。
     
     不要直接在github平台上创建分支，上述命令会自动创建分支。

#### 恢复安装
	新设备中创建准备存储源代码的目录，进入目录，依次执行以下命令：
	```
	git clone -b hexo [github repository] ./
	npm install
	```
	确保新设备已安装hexo-cli、hexo-deployer-git
	最后`hexo g`、`hexo s`
#### 同步代码
	- 在任意一个终端更新博客前执行 `git pull origin hexo` 拉取云端最新版本；
	- 在任意一个终端更新博客后依次执行
	```
	git add .
	git commit -m "更新信息"
	git push origin hexo
	```