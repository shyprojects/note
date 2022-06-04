# Git常用命令

| 命令名称                             | 作用           |
| ------------------------------------ | -------------- |
| git config --global user.name 用户名 | 设置用户签名   |
| git config --global user.email 邮箱  | 设置用户签名   |
| git init                             | 初始化本地库   |
| git status                           | 查看本地库状态 |
| git add 文件名                       | 添加到暂存区   |
| git commit -m "日志信息" 文件名      | 提交到本地库   |
| git reflog                           | 查看历史记录   |
| git reset --hard 版本号              | 版本穿梭       |

## 设置用户签名

```
git config --global user.name shy

git config --global user.email shy@123.com
```

说明：

* 签名的作用是区分不同操作者身份。用户的签名信息在每一个版本的提交信息中能够看
  到，以此确认本次提交是谁做的。Git 首次安装必须设置一下用户签名，否则无法提交代码。
* 这里设置用户签名和将来登录 GitHub（或其他代码托管中心）的账号没有任何关系。

## 初始化本地库

* git init

![image-20220422222138224](Git.assets\image-20220422222138224.png)

## 查看本地库状态

* git status

![image-20220422222710978](Git.assets\image-20220422222710978.png)

在本地库中创建一个文件hello.txt

![image-20220422223113248](Git.assets\image-20220422223113248.png)

查看本地库状态

![image-20220422223240905](Git.assets\image-20220422223240905.png)

## 添加暂存区

* git add

![image-20220422223355368](Git.assets\image-20220422223355368.png)

查看本地库状态

![image-20220422223557872](Git.assets\image-20220422223557872.png)

移除

![image-20220422223634949](Git.assets\image-20220422223634949.png)

移除后只是在暂存区不存在hello.txt

hello.txt依然在工作区存在

## 提交本地库

将暂存区的hello.txt提交到本地库

* git commit -m "日志信息" 文件名

![image-20220422224852711](Git.assets\image-20220422224852711.png)

## 历史版本

* git reflog：查看版本信息
* git log：查看详情版本信息

## 版本穿梭

* git reset --hard 版本号

# 分支

## 常用指令

* git branch -v：查看分支
* git branch 分支名：创建分支
* git checkout 分支名：切换分支
* git merge 分支名：将某分支合并到当前分支

# GitHub

## 创建远程库

![image-20220423010950729](Git.assets\image-20220423010950729.png)

## 创建远程库别名

查看所有别名

* git remote -v

  没有别名

![image-20220423011144852](Git.assets\image-20220423011144852.png)

创建别名

* git remote add 别名 远程库地址

![image-20220423011600800](Git.assets\image-20220423011600800.png)

## 推送本地库到远程库

* git push 别名/远程库地址 分支名

![image-20220423164501424](Git.assets\image-20220423164501424.png)

## 拉取远程库的代码到本地库

* git pull 别名/远程库名 分支名

![image-20220423165051780](Git.assets\image-20220423165051780.png)

拉取远程库的代码成功后已经提交到了本地库。

## 克隆远程库到本地库

* git clone 远程库地址

![image-20220423171413471](Git.assets\image-20220423171413471.png)

## 团队内协作

* 添加其他成员到远程库

![image-20220423173054495](D:\note_Typora\Git.assets\image-20220423173054495.png)

![image-20220423173142761](D:\note_Typora\Git.assets\image-20220423173142761.png)

![image-20220423173154730](D:\note_Typora\Git.assets\image-20220423173154730.png)

邀请后会生成一个邀请函

![image-20220423173339031](D:\note_Typora\Git.assets\image-20220423173339031.png)

https://github.com/shyproject/git-demo/invitations

* 将邀请函发送给好友

## 跨团队协作

非团队的人员对代码进行修改

![image-20220423180709880](D:\note_Typora\Git.assets\image-20220423180709880.png)

![image-20220423180732040](D:\note_Typora\Git.assets\image-20220423180732040.png)

* 进行修改

![image-20220423181009535](D:\note_Typora\Git.assets\image-20220423181009535.png)

* 修改后发送推送的请求

![image-20220423181109448](D:\note_Typora\Git.assets\image-20220423181109448.png)

* 对方同意后会推送到对方的远程库

## 添加SSH免密登录协议

* 在windows家目录下![image-20220423181828838](D:\note_Typora\Git.assets\image-20220423181828838.png)

* 三次回车

![image-20220423181900573](D:\note_Typora\Git.assets\image-20220423181900573.png)

![image-20220423181916061](D:\note_Typora\Git.assets\image-20220423181916061.png)

![image-20220423182022173](D:\note_Typora\Git.assets\image-20220423182022173.png)

* ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDKBPdvpfyuZBAgzWA3+PCrAHEwJ9HO2jMpfpFuDmRoChi81mQK+eU1rOb53heAHFFOXlEWvkP438it1+oLUK/J4F3Rw2H0AvXYb1wniGN4pGymgPtmSPDJ+Ft6GGAbe+q6SacMZEdmkb5CeWUnfhImi3xKvDuQGnFYKxZqskRK2hOVmRcAHd/YVAfxEPxjv0/9WiAgmrjlWSUMTWskeiWDvtwSwxFuxwR+VB2nGCGWNPkshiaAcTEOy8DOBG5oGAY8/fR54uT/hyQjYPCoYKbCLpkIoBlH2daN+CZclhl7CpiGAgQl+M+YrpBIBiEyQ67wUbNnW+g/Vnp/j8jU06qccTRp1XcMEpW4W1rRaKFh8Ih5AkoF5xW0jSDatx1A0SGaUIPjKBmB8xOaotxPiFnJHcJsuEEMmDWLja3ptM3AZrxnfJGzLAWwJkNolIUgpJfhEL2VvTYw0d4ONZDVfsRiqYbLzWpxencmsIqf0MSkSGlnb4vP2zBdHg7o3F9XHWE= shy@123.com

![image-20220423182232952](D:\note_Typora\Git.assets\image-20220423182232952.png)

![image-20220423182255977](D:\note_Typora\Git.assets\image-20220423182255977.png)

* 添加公钥

![image-20220423182430785](D:\note_Typora\Git.assets\image-20220423182430785.png)

![image-20220423182356713](D:\note_Typora\Git.assets\image-20220423182356713.png)

* 使用ssh拉取推送代码

![image-20220423183139784](D:\note_Typora\Git.assets\image-20220423183139784.png)

# IDEA集成git

## 配置git忽略文件

* idea中有一些文件 .xml .iml 与项目无关，为了兼容其他开发工具，对git进行配置，忽略这些文件

创建git.ignore 在家目录下

```ignore
# Compiled class file
*.class

# Log file
*.log

# BlueJ files
*.ctxt

# Mobile Tools for Java (J2ME)
.mtj.tmp/

# Package Files #
*.jar
*.war
*.nar
*.ear
*.zip
*.tar.gz
*.rar

#  virtual  machine  crash  logs,  see
http://www.java.com/en/download/help/error_hotspot.xml
hs_err_pid*

.classpath
.project
.settings
target
.idea
*.iml
```

* 在.gitconfig文件中引用上述文件

```gitconfig
[user]
	name = shy
	email = shy@123.com
[core]
	excludesfile = C:/Users/666/git.ignore
```

* 在idea中定位git

![image-20220423220525591](Git.assets\image-20220423220525591.png)

## 初始化&添加&提交操作

* 在idea中将当前项目初始化本地库：

![image-20220423220907019](Git.assets\image-20220423220907019.png)

* 添加暂存区

![image-20220423222102992](Git.assets\image-20220423222102992.png)

* 提交本地库

![image-20220423222252382](Git.assets\image-20220423222252382.png)

![image-20220423222325437](Git.assets\image-20220423222325437.png)

## 版本穿梭

![image-20220423223558905](Git.assets\image-20220423223558905.png)

* 切换版本

![image-20220423223623880](Git.assets\image-20220423223623880.png)

## 创建切换分支

* 创建分支

![image-20220423224928434](Git.assets\image-20220423224928434.png)

或者

![image-20220423225008614](Git.assets\image-20220423225008614.png)

* 切换分支

![image-20220423225123263](Git.assets\image-20220423225123263.png)

## 合并分支

![image-20220423225633061](Git.assets\image-20220423225633061.png)

* 合并冲突的情况

![image-20220423230135496](Git.assets\image-20220423230135496.png)

![image-20220423230246717](Git.assets\image-20220423230246717.png)

# IDEA集成GitHub

## 设置GitHub账号

* 设置GitHub账号

![image-20220423232849484](Git.assets\image-20220423232849484.png)

* 使用口令登录

![image-20220423233448902](Git.assets\image-20220423233448902.png)

![image-20220423233513519](Git.assets\image-20220423233513519.png)

![image-20220423233813158](Git.assets\image-20220423233813158.png)

![image-20220423233948525](Git.assets\image-20220423233948525.png)

![image-20220423234015857](Git.assets\image-20220423234015857.png)

## 分享项目到远程库

* 该操作相当于创建远程库，将本地库推送到远程库

![image-20220424001344019](Git.assets\image-20220424001344019.png)

![image-20220424001507464](Git.assets\image-20220424001507464.png)

## 推送本地库到远程库

![image-20220424002632209](Git.assets\image-20220424002632209.png)

![image-20220424002747332](Git.assets\image-20220424002747332.png)

## 拉取远程库到本地库

![image-20220424003252823](Git.assets\image-20220424003252823.png)

## 克隆远程库到本地库

![image-20220424003915390](Git.assets\image-20220424003915390.png)

![image-20220424004008549](Git.assets\image-20220424004008549.png)

# 码云-gitee的使用

## 创建远程库

![image-20220424114311095](Git.assets\image-20220424114311095.png)

## 导入GitHub项目

![image-20220424140548517](Git.assets\image-20220424140548517.png)

* 与GitHub同步

![image-20220424140742463](Git.assets\image-20220424140742463.png)

# GitLab

