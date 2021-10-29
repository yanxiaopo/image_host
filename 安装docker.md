# 安装docker

> 环境准备

1.CentOs 7

2.XShell 连接远程服务器

```shell
# 查看系统内核
[root@iZ0tlrunv71vzsZ ~]# uname -r
3.10.0-514.6.2.el7.x86_64  

```

```shell
# 系统版本
[root@iZ0tlrunv71vzsZ ~]# cat /etc/os-release 
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```



> 安装

帮助文档

```shell
# 1.删除旧的版本
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
# 2.需要的安装包
yum install -y yum-utils
# 3.设置镜像的仓库
 sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo # 默认是国外的,十分慢
    
 sudo yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo #阿里云的镜像地址
# 更新yum软件包索引
yum makecache fast
# 4.安装docker docker-ce 社区版 ee企业版 
yum install docker-ce docker-ce-cli containerd.io
# 5.启动docker 
systemctl start docker
# 6.检查docker 是否安装成功  
docker version
# 7.docker run hello-world

# 8.查看下在的docker镜像
[root@iZ0tlrunv71vzsZ ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED       SIZE
hello-world   latest    feb5d9fea6a5   4 weeks ago   13.3kB


```

卸载docker

```shell
# 1.卸载依赖
yum remove docker-ce docker-ce-cli containerd.io
# 2.删除资源
rm -rf /var/lib/docker #docker 的默认工作路径
```



## 阿里云镜像加速

1.找镜像加速地址

![image-20211025233442527](C:\Users\yxs13\AppData\Roaming\Typora\typora-user-images\image-20211025233442527.png)

2.配置使用

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://u6lhn8t4.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```



## 回顾hello world流程

![image-20211025234939536](C:\Users\yxs13\AppData\Roaming\Typora\typora-user-images\image-20211025234939536.png)

## 底层原理

### Docker是怎么工作的?

Docker是一个client-Server结构的系统,Docker的守护进程运行在主机上.通Socket从客户端访问

DockerServer 接受到Docker-Client的指令,就会执行这个命令!

![image-20211025235422198](C:\Users\yxs13\AppData\Roaming\Typora\typora-user-images\image-20211025235422198.png)

Docker为什么比VM快?

![image-20211025235714805](C:\Users\yxs13\AppData\Roaming\Typora\typora-user-images\image-20211025235714805.png)

1.Dokcer有着比虚拟更少的抽象层

2.Docker利用的是宿主机的内核,vm需要的是Guest OS

新建一个容器的时候,不需要像虚拟机一样重新加载一个操作系统内核,避免引导.虚拟机是加载Guest OS,分钟级别的,而Docker是利用宿主机的操作系统,省略了这个复杂的过程,秒级

![image-20211026000120976](C:\Users\yxs13\AppData\Roaming\Typora\typora-user-images\image-20211026000120976.png)



# Docker的常用命令

## 帮助命令

```shell
docker version # 显示docker的版本信息
docker info    # 显示docker的系统信息(包裹镜像和容器的数量)
docker --help  # 帮助命令

```

帮助文档的地址:https://docs.docker.com/reference/

## 镜像命令

### **docker images**  查看本地所有镜像

```shell
[root@iZ0tlrunv71vzsZ ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED       SIZE
hello-world   latest    feb5d9fea6a5   4 weeks ago   13.3kB

# REPOSITORY 镜像的仓库源
# TAG		 镜像的标签
# IMAGE ID	 镜像id
# CREATED 	 镜像的创建时间
# SIZE 		 镜像的大小

# 可选项
Options:
  -a, --all             # 列出所有镜像
  -q, --quiet           # 只显示镜像的id
 
```

### **docker search**  搜索镜像

```shell
docker search
[root@iZ0tlrunv71vzsZ ~]# docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   11587     [OK]       
mariadb                           MariaDB Server is a high performing open sou…   4407      [OK]    

# 可选项 通过收藏来过滤
--filter=STARS=3000
```

### **docker pull** 下载镜像

```shell
docker pull 镜像名[:tag]

[root@iZ0tlrunv71vzsZ ~]# docker pull mysql
Using default tag: latest   		# 不写tag,默认最新
latest: Pulling from library/mysql  
b380bbd43752: Pull complete 		# 分层下载,docker image的核心 联合文件系统
f23cbf2ecc5d: Pull complete 
30cfc6c29c0a: Pull complete 
b38609286cbe: Pull complete 
8211d9e66cd6: Pull complete 
2313f9eeca4a: Pull complete 
7eb487d00da0: Pull complete 
4d7421c8152e: Pull complete 
77f3d8811a28: Pull complete 
cce755338cba: Pull complete 
69b753046b9f: Pull complete 
b2e64b0ab53c: Pull complete 
Digest: sha256:6d7d4524463fe6e2b893ffc2b89543c81dec7ef82fb2020a1b27606666464d87 # 签名
Status: Downloaded newer image for mysql:latest 
docker.io/library/mysql:latest		# 真实地址

# docker pull mysql 等价于 docker pull docker.io/library/mysql:latest

# 指定版本下载
docker pull mysql:5.7

[root@iZ0tlrunv71vzsZ ~]# docker pull mysql:5.7
5.7: Pulling from library/mysql
b380bbd43752: Already exists  	# 只下载不同的依赖模块(联合文件系统)
f23cbf2ecc5d: Already exists 
30cfc6c29c0a: Already exists 
b38609286cbe: Already exists 
8211d9e66cd6: Already exists 
2313f9eeca4a: Already exists 
7eb487d00da0: Already exists 
a71aacf913e7: Pull complete 
393153c555df: Pull complete 
06628e2290d7: Pull complete 
ff2ab8dac9ac: Pull complete 
Digest: sha256:2db8bfd2656b51ded5d938abcded8d32ec6181a9eae8dfc7ddf87a656ef97e97
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7

```

### **docker rmi**  删除镜像

```shell
docker rmi -f 938b57d64674(镜像id) # 删除指定的镜像

docker rmi -f 镜像id 镜像id 镜像id 镜像id # 删除多个镜像

docker rmi -f $(docker images -aq) # 删除全部镜像

```



## 容器命令

### **有了镜像才可以创建容器,下载一个centos镜像来测试学习**

```shell
docker pull centos
```

### **新建容器并启动**

```shell
docker run [可选参数] image

# 参数说明
--name="Name" 容器名字 tomcat01 tomcat02,用来区分容器
-d 		      后台方式运行
-it		      使用交互方式运行,进入容器查看内容		      
-p			  指定容器的端口 -p 8080:8080
	-p ip:主机端口:容器端口
	-p 主机端口:容器端口 (常用)
	-p 容器端口
	容器端口
-P			  随机指定端口

# 测试,启动并进入容器
[root@iZ0tlrunv71vzsZ ~]# docker run -it centos /bin/bash
[root@bb7f9023cf3c /]# 
# 从容器退回主机
exit

```

### **列出所有运行中的容器**

```shell
# docker ps 命令
	# 列出当前正在运行的容器
-a	# 列出当前正在运行的容器+带出历史运行过的容器
-n=?# 列出最近创建的容器	
-q  # 只显示容器的编号

```

### **退出容器**

```shell
exit # 退出容器并停止
ctrl+P+Q # 退出但不停止
```

### **删除容器**

```shell
docker rm 容器id # 删除指定的容器,不能删除正在运行的容器,如果强制删除 rm -f

docker rm -f $(docker ps -aq) # 删除所有容器

docker ps -a -q|xargs docker rm # 删除所有容器

```

### **启动和停止容器**

```shell
docker start 容器id 	# 启动容器
docker restart 容器id # 重启容器
docker stop 容器id 	# 停止当前正在运行的容器	
docker kill 容器id	# 强制停止当前正在运行的容器
```



## 常用其他命令

### 后台启动容器

```shell
# 通过docker run -d 镜像名
docker run -d centos  
# 发现centos 停止了
# 容器使用后台运行,就必须要有一个前台进程,docker发现没有应用了,就会自动停止
# nginx,容器启动后发现自己没有提供服务,就会立即停止,没有正在运行的程序

```

### 查看日志

```shell
docker logs -f -t --tail 容器,没有日志
# 自己编写一段shell脚本
"while true;do echo xiaopo;sleep 1;done"

# 运行centos 并带脚本执行
docker run -d centos /bin/sh -c "while true;do echo xiaopo;sleep 1;done"

# docker logs -f -t --tail 10 容器id
	-f 	 	# 是持续输出日志内容
	-t  	# 是显示时间戳
	--tail  # 要显示的日志条数
```

### 查看容器中的进程信息

```shell
# docker top 容器id
docker top f699736231c3
UID                 PID                 PPID                C                   STIME     
root                29624               29602               0                   00:27     
root                31490               29624               0                   00:57     

```

### 镜像元数据

```shell
# docker inspect 容器id
[
    {
        "Id": "f699736231c3c62ea8c4f2f7bd9d6afda12b1ae2accb502cd320ad7180cdf6a6",
        "Created": "2021-10-26T16:27:17.938209253Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo xiaopo;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 29624,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2021-10-26T16:27:18.231889667Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:5d0da3dc976460b72c77d94c8a1ad043720b0416bfc16c52c45d4847e53fadb6",
        "ResolvConfPath": "/var/lib/docker/containers/f699736231c3c62ea8c4f2f7bd9d6afda12b1ae2accb502cd320ad7180cdf6a6/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/f699736231c3c62ea8c4f2f7bd9d6afda12b1ae2accb502cd320ad7180cdf6a6/hostname",
        "HostsPath": "/var/lib/docker/containers/f699736231c3c62ea8c4f2f7bd9d6afda12b1ae2accb502cd320ad7180cdf6a6/hosts",
        "LogPath": "/var/lib/docker/containers/f699736231c3c62ea8c4f2f7bd9d6afda12b1ae2accb502cd320ad7180cdf6a6/f699736231c3c62ea8c4f2f7bd9d6afda12b1ae2accb502cd320ad7180cdf6a6-json.log",
        "Name": "/keen_cray",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/9249a9f8cdf0dd5fa148315c161006f19d0c995f27406671ebc680ffbda82afb-init/diff:/var/lib/docker/overlay2/1b78187de293cd1df1e7525e386752284d8c9bbf91b61bacdd2ee2e97091d266/diff",
                "MergedDir": "/var/lib/docker/overlay2/9249a9f8cdf0dd5fa148315c161006f19d0c995f27406671ebc680ffbda82afb/merged",
                "UpperDir": "/var/lib/docker/overlay2/9249a9f8cdf0dd5fa148315c161006f19d0c995f27406671ebc680ffbda82afb/diff",
                "WorkDir": "/var/lib/docker/overlay2/9249a9f8cdf0dd5fa148315c161006f19d0c995f27406671ebc680ffbda82afb/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "f699736231c3",
            "Domainname": "",
            "User": "",
            "AttachStdin": true,
            "AttachStdout": true,
            "AttachStderr": true,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true;do echo xiaopo;sleep 1;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20210915",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "890c4d719bded9d9e5504ca178b118c8a38d18ffa300108bc0eeee7120b587a7",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/890c4d719bde",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "6a2b2479851f17cc58847072ec6a9355970ad6251f90ba4a912a86edb7704a00",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "02285e0039ef2977e6f951afb74b3d8a700b7e67aefaadec9b7b8d1daea40529",
                    "EndpointID": "6a2b2479851f17cc58847072ec6a9355970ad6251f90ba4a912a86edb7704a00",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]

```

### 进入当前正在运行的容器

```shell
# 我们通常都是使用后台方式运行的,需要进入容器,修改一些配置

# 命令
docker exec -it 容器id bashShell
# 方式2
docker attach 容器id

# docker exec   # 进入容器后开启一个新的终端
# docker attach # 进入容器正在执行的终端


```

### 从容器内拷贝文件到主机

```shell
docker cp 容器id:容器内路径 主机路径
# 容器内新建文件
[root@f699736231c3 /]# cd home/
[root@f699736231c3 home]# touch xiaopo.java
[root@f699736231c3 home]# ls
xiaopo.java
[root@f699736231c3 home]# exit
exit
# 退出后拷贝到主机上
[root@iZ0tlrunv71vzsZ home]# docker cp f699736231c3:/home/xiaopo.java /home
[root@iZ0tlrunv71vzsZ home]# ls
xiaopo.java

# 拷贝是一个手动过程,未来我们使用 -v 卷技术,可以实现自动同步
```





## 启动一个nginx

> Docker 安装Nginx

```
# 1.搜索镜像
# 2.下载镜像 
docker pull nginx
# 3.启动镜像
docker run -d --name nginx01 -p 3344:80 nginx
# -d 后台运行
# -p 端口映射 3344(宿主机端口) 80(容器内端口)


```

进入容器

```
docker exec -it nginx01 /bin/bash
```



### 启动一个tomcat



```
# 官方的提供的命令
docker run -it --rm tomcat:9.0
# 之前的启动都是后台,停止了容器之后,容器还可以查到, --rm 用完即删除(镜像还在,容器销毁)

# 正常的命令
docker pull tomcat:9.0
# 启动运行
docker run -d -p 3355:8080 --name tomcat01
# 显示不了tomcat页面展示404 该镜像是最小镜像,把不必要的都删除了


```



### 部署一个es+kibana

```
# es暴露的端口很多
# es十分的消耗内存
# es的数据要放到安全目录!挂载

 docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:tag
 
# --net somenetwor 网络配置(后面解释)
# "discovery.type=single-node" 集群(默认单个节点)
 docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2
 
# 启动以后linux服务器卡住了  docker status 查看cpu的状态
```



```

```

