---
title: gitlab+jenkins实现自动化流程
published: 2025-05-20
description: 通过配置 GitLab + Jenkins Webhook实现持续集成。
tags: [Markdown, Blogging]
category: Examples
draft: false
---

> 通过配置 GitLab + Jenkins Webhook，GitLab 中的代码提交或合并请求等事件可以自动触发 Jenkins 的构建任务，实现持续集成。借助此机制，开发者可以减少手动操作，提升项目的自动化程度和团队协作效率。

### 什么是Webhook？

Webhook 是一种基于 HTTP 的回调机制。当事件发生时，服务端会将该事件的信息通过 HTTP POST 请求的形式推送到预先设置的 Webhook URL。这种方式不需要客户端不断轮询服务端的状态，而是服务端在事件发生时直接将数据发送到客户端。这不仅节省了系统资源，还保证了数据的实时性。

GitLab 和 GitHub 都提供了 Webhook 功能，使得外部系统可以接收来自代码库的实时事件通知，比如代码推送、合并请求。在CI/CD自动化流程中常用的场景：当代码库中的代码更新时，自动触发 CI/CD 工具（如 Jenkins、GitLab CI、GitHub Actions 等）来进行代码构建、测试和部署。

### GitLab+Jenkins配置Webhook触发任务构建

在使用 GitLab 和 Jenkins 进行持续集成和自动化部署时，Webhook 可以用来触发 Jenkins 任务（Job），当 GitLab 中的代码发生变更时，会自动通知 Jenkins 运行预设的构建流程。这种方式可以提高项目的开发效率和构建的自动化程度。

前提条件：

GitLab 和 Jenkins 服务均已搭建完成

GitLab服务器和Jenkins服务器网络能够相互ping通

#### Step1：在Jenkins中安装GitLab插件

进入到Jenkins系统管理->插件管理页面，搜索下载gitlab插件
![进入到Jenkins系统管理->插件管理页面，搜索下载gitlab插件](/src/images/image.png)

### Step2：在Jenkins中创建并配置任务
新建任务：

打开 Jenkins 主页面，点击“新建任务”。

输入项目名称，选择“构建一个自由风格的软件项目”或“Pipeline”项目，点击确定。

配置源码管理：

在 源码管理 一栏中选择 Git。

在 Repository URL 中填写 GitLab 仓库的 URL 地址。

填写 GitLab 的登录凭证（即 GitLab 的用户名和访问 Token），以确保 Jenkins 有权限访问该仓库。
![配置源码管理](/src/images/image-1.png)

构建触发器：

勾选 Build when a change is pushed to GitLab

点击Generate生成Secret Token，这个后续需要填写到Gitlab中用来鉴权
![勾选 Build when a change is pushed to GitLab](/src/images/image-2.png)
![点击Generate生成Secret Token](/src/images/image-3.png)

### Step3：在GitLab中配置Webhook
打开GitLab项目，进入到设置->Webhooks->添加新的webhook：
![在GitLab中配置Webhook](/src/images/image-4.png)

在 URL 输入框中，填写 Jenkins 的 Webhook 接口地址，格式如下：
```
http://<JENKINS_URL>/project/<JOB_NAME>
```

例如，如果 Jenkins 的地址是 http://jenkins.example.com，Job 名称为 my-project，则填写的 URL 为 http://jenkins.example.com/project/my-project。

另外填写Secret令牌，也就是之前在Jenkins端生成的**Secret Token**
![填写Secret令牌](/src/images/image-5.png)

选择适合的触发事件（推送事件、标签推送事件、合并请求事件）:
![选择适合的触发事件](/src/images/image-6.png)

保存之后可以点击测试下：
![点击测试](/src/images/image-7.png)

如果显示都是200代表正常：
![显示都是200代表正常](/src/images/image-8.png)

接下来通过通过git push提交代码库（或者可以提交MR），可以看到Jenkins端任务已经被GitLab webhook触发
![Jenkins端任务已经被GitLab webhook触发](/src/images/image-9.png)