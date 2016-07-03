# Tutorial for Kubernetes

## Docker Image

Official image: [gogs/gogs](https://hub.docker.com/r/gogs/gogs/)

## Deploy

Create _replication controller_ manifest to install

    [vagrant@localhost gogs]$ kubectl create -f kubernetes-manifests/gogs-controller.yaml --namespace=harpoon
    replicationcontroller "gogs" created

    [vagrant@localhost gogs]$ kubectl --namespace=harpoon get pods,rc
    NAME         READY     STATUS    RESTARTS   AGE
    gogs-a0z6y   1/1       Running   0          29s
    NAME         DESIRED   CURRENT   AGE
    gogs         1         1         30s

Create *service* manifest to install
 
    [vagrant@localhost gogs]$ kubectl create -f kubernetes-manifests/gogs-service.yaml --namespace=harpoon
    You have exposed your service on an external port on all nodes in your
    cluster.  If you want to expose this service to the external internet, you may
    need to set up firewall rules for the service port(s) (tcp:32130,tcp:32129) to serve traffic.
    
    See http://releases.k8s.io/HEAD/docs/user-guide/services-firewalls.md for more details.
    service "gogs" created

    [vagrant@localhost gogs]$ kubectl --namespace=harpoon get ep,svc
    NAME      ENDPOINTS                         AGE
    gogs      172.17.0.20:22,172.17.0.20:3000   20m
    NAME      CLUSTER-IP                        EXTERNAL-IP   PORT(S)           AGE
    gogs      10.3.0.76                         <nodes>       3000/TCP,22/TCP   20m

## Set up gogs

* Figure: First browsing

![install](/kubernetes-manifests/img/屏幕快照%202016-07-03%20上午9.50.36.png)

* Figure: Using SQLite3 backend

![database](/kubernetes-manifests/img/屏幕快照%202016-07-03%20上午9.51.57.png)

* Figure: Setup networking

![networking](/kubernetes-manifests/img/屏幕快照%202016-07-03%20上午10.10.37.png)

* Figure: Welcome to login

![login](/kubernetes-manifests/img/屏幕快照%202016-07-03%20上午10.12.21.png)

## Guide

### Sign

* Figure: Sign up

![signup](/kubernetes-manifests/img/屏幕快照%202016-07-03%20上午10.14.23.png)

* Figure: Sign in

![signin](/kubernetes-manifests/img/屏幕快照%202016-07-03%20上午10.15.46.png)

![home](/kubernetes-manifests/img/屏幕快照%202016-07-03%20上午10.18.32.png)

### Try

* Figure: Init repo

![initrepo](/kubernetes-manifests/img/屏幕快照%202016-07-03%20上午10.18.32.png)

![initrepo](/kubernetes-manifests/img/屏幕快照%202016-07-03%20上午10.19.10.png)

* Clone, local edit, Add and Commit

Clone Repo

    [vagrant@localhost gogs]$ git clone http://172.17.4.50:32130/tangfx/hello-gogs /tmp/tangfx/hello-gogs
    正克隆到 '/tmp/tangfx/hello-gogs'...
    remote: Counting objects: 5, done.
    remote: Compressing objects: 100% (4/4), done.
    remote: Total 5 (delta 0), reused 0 (delta 0)
    展开对象中: 100% (5/5), 完成.
    检查连接... 完成。

Do a helloworld Golang app

    [vagrant@localhost hello-gogs]$ vim main.go
    [vagrant@localhost hello-gogs]$ env GOPATH=/tmp go run main.go
    Hello world!

Push into remote

    [vagrant@localhost hello-gogs]$ git status
    位于分支 master
    您的分支与上游分支 'origin/master' 一致。
    未跟踪的文件:
      （使用 "git add <文件>..." 以包含要提交的内容）
    
        	main.go

    提交为空，但是存在尚未跟踪的文件（使用 "git add" 建立跟踪）
    [vagrant@localhost hello-gogs]$ git add main.go
    [vagrant@localhost hello-gogs]$ git commit -a -m "main.go"
    
    *** Please tell me who you are.
    
    Run
    
      git config --global user.email "you@example.com"
      git config --global user.name "Your Name"
    
    to set your account's default identity.
    Omit --global to set the identity only in this repository.
    
    fatal: empty ident name (for <vagrant@localhost.(none)>) not allowed

Git require account config before commit, commit again
 
    [vagrant@localhost hello-gogs]$ git config user.email "fxtang@qingyuanos.com"
    [vagrant@localhost hello-gogs]$ git config user.name "tangfx"
    [vagrant@localhost hello-gogs]$ git commit -a -m "main.go"
    [master 2bbda58] main.go
     1 file changed, 7 insertions(+)
     create mode 100644 main.go

At last, push

    [vagrant@localhost hello-gogs]$ git push
    warning: push.default 尚未设置，它的默认值在 Git 2.0 已从 'matching'
    变更为 'simple'。若要不再显示本信息并保持传统习惯，进行如下设置：
    
      git config --global push.default matching
    
    若要不再显示本信息并从现在开始采用新的使用习惯，设置：
    
      git config --global push.default simple
    
    当 push.default 设置为 'matching' 后，git 将推送和远程同名的所有
    本地分支。
    
    从 Git 2.0 开始，Git 缺省采用更为保守的 'simple' 模式，只推送当前
    分支到远程关联的同名分支，即 'git push' 推送当前分支。
    
    参见 'git help config' 并查找 'push.default' 以获取更多信息。
    （'simple' 模式由 Git 1.7.11 版本引入。如果您有时要使用老版本的 Git，
    为保持兼容，请用 'current' 代替 'simple'）
    
    Username for 'http://172.17.4.50:32130': tangfx
    Password for 'http://tangfx@172.17.4.50:32130': 
    对象计数中: 3, 完成.
    压缩对象中: 100% (3/3), 完成.
    写入对象中: 100% (3/3), 333 bytes | 0 bytes/s, 完成.
    Total 3 (delta 1), reused 0 (delta 0)
    To http://172.17.4.50:32130/tangfx/hello-gogs
       329a1c3..2bbda58  master -> master

* Figure: updated

![commit](/kubernetes-manifests/img/屏幕快照%202016-07-03%20上午10.26.48.png)

![content](/kubernetes-manifests/img/屏幕快照%202016-07-03%20上午10.27.06.png)
