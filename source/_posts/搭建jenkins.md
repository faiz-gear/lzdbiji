---
title:  搭建jenkins服务器环境
tag: jenkins
keywords: jenkins
categories: 自动化部署
---

## 一. 购买云服务器

这里我购买的服务器是阿里云的ECS云服务器，系统是CentOS Linux release 8.5.2111，怎么购买自行百度或直接去官网购买

## 二. 搭建服务器环境

### 2.1. jenkins自动化部署

#### 2.1.1. 安装Java环境

Jenkins本身是依赖Java的，所以我们需要先安装Java环境：

* 这里我安装了Java1.8的环境

```shell
dnf search java-1.8
dnf install java-1.8.0-openjdk.x86_64
```



#### 2.1.2. 安装Jenkins

因为Jenkins本身是没有在dnf的软件仓库包中的，所以我们需要连接Jenkins仓库：

* wget是Linux中下载文件的一个工具，-O表示输出到某个文件夹并且命名为什么文件；
* rpm：全称为**The RPM Package Manage**，是Linux下一个软件包管理器；

```shell
wget –O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo

# 导入GPG密钥以确保您的软件合法
rpm --import https://pkg.jenkins.io/redhat/jenkins.io.key
# 或者
rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key
```

编辑一下文件/etc/yum.repos.d/jenkins.repo

* 可以通过vim编辑

```
[jenkins]

name=Jenkins-stable

baseurl=http://pkg.jenkins.io/redhat

gpgcheck=1
```

安装Jenkins

```shell
dnf install jenkins # --nogpgcheck(可以不加)
```

启动Jenkins的服务：

```shell
systemctl start jenkins
systemctl status jenkins
systemctl enable jenkins
```

Jenkins默认使用8080端口提供服务，所以需要加入到安全组中：

![image-20201204173117359](https://tva1.sinaimg.cn/large/0081Kckwgy1glbyeoz6tqj315w0bzmzj.jpg)





#### 2.1.3. Jenkins用户

我们后面会访问centos中的某些文件夹，默认Jenkins使用的用户是 `jenkins`，可能会没有访问权限，所以我们需要修改一下它的用户：

修改文件的路径：`/etc/sysconfig/jenkins`

![image-20210825162827962](https://tva1.sinaimg.cn/large/008i3skNgy1gtt46oxg53j60n00aw75302.jpg)

之后需要重启一下Jenkins：

```shell
systemctl restart jenkins
```



#### 2.1.4. Jenkins配置

打开浏览器，输入：http://8.134.60.235:8080/

* 注意：你输入自己的IP地址

获取输入管理员密码：

* 在下面的地址中 `cat /var/lib/jenkins/secrets/initialAdminPassword`

![image-20201203173047824](/Users/coderwhy/Library/Application Support/typora-user-images/image-20201203173047824.png)

可以安装推荐的插件：

![安装推荐的插件](https://tva1.sinaimg.cn/large/0081Kckwgy1glbylb26ouj30fb082js7.jpg)



#### 2.1.5. Jenkins任务

**新建任务：**

![新建任务](https://tva1.sinaimg.cn/large/0081Kckwgy1glc0uinjzej30pa0fet9z.jpg)

![image-20201204185613061](https://tva1.sinaimg.cn/large/0081Kckwgy1glc0v225vaj30wl0hkn05.jpg)

**配置项目和保留策略：**

![image-20210825160744119](https://tva1.sinaimg.cn/large/008i3skNgy1gtt3l4fjm6j614p0d2dgl02.jpg)

**源码管理：**

![image-20210825160818122](https://tva1.sinaimg.cn/large/008i3skNgy1gtt3lpcvtgj614k0j8jsn02.jpg)

**构建触发器：**

这里的触发器规则是这样的：

* 定时字符串从左往右分别是：分 时 日 月 周

```js
#每半小时构建一次OR每半小时检查一次远程代码分支，有更新则构建
H/30 * * * *

#每两小时构建一次OR每两小时检查一次远程代码分支，有更新则构建
H H/2 * * *

#每天凌晨两点定时构建
H 2 * * *

#每月15号执行构建
H H 15 * *

#工作日，上午9点整执行
H 9 * * 1-5

#每周1,3,5，从8:30开始，截止19:30，每4小时30分构建一次
H/30 8-20/4 * * 1,3,5
```



![触发器](https://tva1.sinaimg.cn/large/008i3skNgy1gtt419gw6tj614g0agmy402.jpg)

**构建环境：**

注意：我们需要搭建Node的环境

* 第一步：配置Node的环境；
* 第二步：安装Node的插件；

![image-20201204190055096](https://tva1.sinaimg.cn/large/0081Kckwgy1glc0zyecd4j31ar0ecjuz.jpg)

第一步：配置Node的环境

![node环境](https://tva1.sinaimg.cn/large/008i3skNgy1gtt42elwduj614h0elwfq02.jpg)

第二步：安装Node的插件

* 这里因为我已经安装过了，所以没有搜索到；

![image-20201204185949452](https://tva1.sinaimg.cn/large/0081Kckwgy1glc0yta64bj30r907caam.jpg)



构建执行的任务：

* 查看Node的版本等是否有问题；
* 执行 `npm install` 安装项目的依赖；
* 移除原来mall_cms文件的所有内容；
* 将打包的dist文件夹内容移动到mall_cms文件夹；

```shell
pwd
node -v
npm -v

npm install 
npm run build

pwd

echo '构建成功'

ls

# 删除/root/mall_cms文件夹里所有的内容
rm -rf /root/mall_cms/* 

cp -rf ./dist/* /root/mall_cms/
```



![执行构建任务](https://tva1.sinaimg.cn/large/008i3skNgy1gtt43sz933j614o0dy0tk02.jpg)



### 2.2. nginx安装和配置

#### 2.2.1. 安装nginx

后续我们部署会使用nginx，所以需要先安装一下nginx：

```shell
dnf install nginx
```

启动nginx：

```shell
systemctl start nginx
systemctl status nginx
systemctl enable nginx
```



#### 2.2.2. 配置nginx

我们这里主要配置nginx的用户和默认访问目录：

配置用户：

![image-20210825163329209](https://tva1.sinaimg.cn/large/008i3skNgy1gtt4bwvxc4j60f30453yk02.jpg)



通过Linux命令创建文件夹和文件：

```shell
mkdir /root/mall_cms
cd /root/mall_cms
touch index.html

vi index.html
```



配置访问目录：

![image-20210825163406566](https://tva1.sinaimg.cn/large/008i3skNgy1gtt4cka6hgj60n00bwgmc02.jpg)



