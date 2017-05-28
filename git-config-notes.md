感谢[廖雪峰老师的教程](http://www.liaoxuefeng.com/)。

普通OIer，用Git主要是为了快速更新/迁移代码库，以便在不同的机器上查用。

几点注意，

- Git是分布式版本控制系统，但只能监视纯文本文件的版本历史，各类文档、媒体等二进制文件只保留历史文件快照，无法比较历史版本。
- GitHub是提供Git仓库托管服务的网站，但托管私有项目需要交保护费。
- 关于姓名和邮箱设置，挖坑。



### macOS设置

---

下面假设macOS已经配置好了使用zsh的iTerm2（关于如何配置，请看[这里](https://github.com/Hakuwannng/iTerm2-zsh-configuration-steps/blob/master/iterm2-zsh.md)）。

Xcode自带了git，版本一般不是最新，但一定是被验证过的稳定版本。用如下命令查看当前git版本（没有以▶开头的行是系统输出或文件内容）：

```powershell
▶ git --version
git version 2.11.0 (Apple Git-81)
```

笔者当前的系统版本是macOS Sierra 10.12.5，第二行的注释就是当前的git版本号。

接下来用Homebrew更新git（可略过）。

假设机器上已经有了Homebrew。输入如下命令，

```powershell
▶ brew install git
#安装的时候注意记录brew下来的git版本号
```

但是系统自带的git被放在`usr/bin/`目录下，而通过Homebrew安装的git被放在`/usr/local/bin/`目录。在用户权限被限制、无法直接修改前者目录的新macOS下，我们不能覆盖更新，只能通过其他方式告诉系统，有新版git可以调用。有以下几种方式可以尝试，

```powershell
#输入以下命令，查看当前系统路径
▶ echo $PATH
#返回的一串路径以英文半角冒号隔开，检查是否有/usr/local/bin和/usr/local/git/bin

#若没有，可修改以下两个文件之一，

#文件一
▶ vi ~/.bash_profile
#在文件头部加入以下两行（若存在就把它们移到文件头部）
export PATH="/usr/local/git/bin:$PATH"
export PATH="/usr/local/bin:$PATH"
▶ source ~/.bash_profile
#使修改立即生效。为了保险，可以先重启iTerm2再进行下一步验证

#文件二
▶ sudo vi /etc/paths
#在文件头部加入以下两行（已存在的不用重复添加）
/usr/local/git/bin
/usr/local/bin
#保存退出，重启iTerm2
```

然后用以下两种方式验证是否完成了更新。

```powershell
#方式一
▶ which git
/usr/local/bin
▶ which -a git
/usr/local/bin
/usr/bin
#这个输出应与上面完全相同

#方式二
▶ git version
git version 2.13.0
#这个输出表示的git版本应与当初记录的brew安装的相同（即最新版本）
```

如果验证失败且确实有更新git的需要，可以参考文末链接的相关博文。方法通常是尝试修改系统权限后暴力覆盖更新，或手动安装（从git官网下载、编译）。

在使用git和GitHub之前，最好先统一和Github账户和本地的用户名/邮箱地址，并上传本地机器的SSH公钥。

注册一个GitHub账号，完成后在iTerm2里执行如下命令，

```powershell
#userName和userName@example.com需替换为自己GitHub账户的姓名（不是用户名）和邮箱
▶ git config --global user.name "userName"
▶ git config --global user.email userName@example.com
```

然后往GitHub上传SSH公钥。先检查本地情况，

```powershell
#先检查本地是否已经存在SSH，SSH相关文件都被存放在~/.ssh中
#若没有这个目录，或执行如下命令后不存在id_rsa和id_rsa.pub文件，则需要生成基于RSA的本地SSH
▶ cd ~/.ssh
▶ ls -a
```
若本地不存在相关目录和文件，可通过如下方式生成，
```powershell
▶ ssh-keygen -t rsa -C "userName@example.com"
#其中-t指定了SSH的种类是rsa；-C表示添加注释（GitHub要求加入邮箱地址作为注释）
#如果没有特殊保密需求，生成过程中遇到yes/no选项的一律输入yes，其余的一路回车即可
```

然后再进入`~/.ssh`目录，不出意外就可以看见`id_rsa.pub`文件。全文拷贝之，在GitHub用户设置（`Settings`）中，点击`SSH and GPG keys`选项卡，在`SSH keys`框中点击`New SSH key`按钮。`Title`栏随意填写（不和自己使用的其它机器混淆即可），Key栏全文粘贴`id_rsa.pub`的内容。完成后点击`Add SSH key`按钮即可。

接下来用git做工作。

新建一个目录，用于存放git库。进入目录后对它初始化。

```powershell
▶ mkdir test
▶ cd ./test
▶ git init

#此时执行如下命令
▶ ls -a
#应出现如下目录
.git
```

