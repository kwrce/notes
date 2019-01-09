# Docker学习

## 介绍

Docker 提供了一个可以运行应用程序的封套(envelope)，或者说容器。它原本是 dotCloud 启动的一个业余项目，并在前些时候开源了。它吸引了大量的关注和讨论，导致 dotCloud 把它重命名到 Docker Inc。

Docker 扩展了 Linux 容器（Linux Containers），或着说 LXC，通过一个高层次的 API 为进程单独提供了一个轻量级的虚拟环境。Docker 利用了 LXC， cgroups 和 Linux 自己的内核。和传统的虚拟机不同的是，一个 Docker 容器并不包含一个单独的操作系统，而是基于已有的基础设施中操作系统提供的功能来运行的。

### Docker类似虚拟机的概念，但是与虚拟化技术的不同点在于下面几点：

1.虚拟化技术依赖物理CPU和内存，是硬件级别的；而docker构建在操作系统上，利用操作系统的containerization技术，所以docker甚至可以在虚拟机上运行。传统的虚拟化技术比较父子称为"系统"，docker是开源的且是轻量化的，可以部署少量应用。

2..传统的虚拟化技术使用快照来保存状态；而docker在保存状态上不仅更为轻便和低成本，而且引入了类似源代码管理机制，将容器的快照历史版本一一记录，切换成本很低。

## 组成：

Docker 由下面这些组成：

1.Docker 服务器守护程序（server daemon），用于管理所有的容器。

2.Docker 命令行客户端，用于控制服务器守护程序。

3.Docker 镜像：查找和浏览 docker 容器镜像。

## docker安装

*注：*

*docker必须安装在centos内核版本在3以上的系统中*

1.安装命令：yum install https://get.docker.com/rpm/1.7.1/centos-6/RPMS/x86_64/docker-engine-1.7.1-1.el6.x86_64.rpm

2.更改配置文件

vi /etc/sysconfig/docker

other-args列更改为：

other_args="--exec-driver=lxc --selinux-enabled"

3.docker启动

\#service docker start

\#chkconfig docker on

## Docker使用

**1.docker镜像的获取与删除**

(1) docker pull centos ：下载centos所有的镜像

(2)docker pull centos:centos6  下载centos6镜像

(3)docker images  查看本机所有的镜像包

(4)docker images -a  列出所有的images（包含历史）

(5)docker 导入镜像

docker load --input ~/centos-7.3.tar

(6)docker挂载主机目录 -v

docker run -i -t -v /root/engine/:/root/engine centos /bin/bash

 (7)docker 容器镜像删除

①.停止所有的container，这样才能够删除其中的images：

docker stop $(docker ps -a -q)

如果想要删除所有container的话再加一个指令：

docker rm $(docker ps -a -q)

②.查看当前有些什么images

docker images

③.删除images，通过image的id来指定删除谁

docker rmi <image id>

想要删除untagged images，也就是那些id为<None>的image的话可以用

docker rmi $(docker images | grep "^<none>" | awk "{print $3}")

要删除全部image的话

docker rmi $(docker images -q)

## Docker镜像导入导出

docker 提供把镜像导出export（保存save）为文件的机制，这样就可以把镜像copy到任意地方了。

**方式一:容器export/import**

格式：docker export CONTAINER(容器)

使用 docker ps -a 查看本机已有的容器.

**方式二:镜像save/load**

格式：docker save IMAGE(镜像)

使用  docker commit <CONTAIN-ID> <IMAGE-NAME>命令把一个正在运行的容器保存为镜像，如：

   (1)首先查看所有的容器信息

（2）使用docker commit命令将指定容器保存为镜像

（3）使用docker images 查看刚才保存的镜像

（4）使用docker run 命令测试刚才保存的镜像是否正确

（5）使用docker save 命令将镜像导出成文件







