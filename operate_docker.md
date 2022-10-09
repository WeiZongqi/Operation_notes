# Operation docker, 参考[docs](https://acute-sleep-247.notion.site/55073e5eb4b249eaa5c8320d114fc8d7)

## 第一部分：toy-example
参考[create first Docker](https://www.freecodecamp.org/news/a-beginners-guide-to-docker-how-to-create-your-first-docker-application-cc03de9b639f/)

## 集群使用
### 第一步：安装docker
windows下安装
1. 建议创建软连接，不然win10下会卡机或者C盘空间不足，后续的镜像默认放到C盘
2. windows下首先安装wsl2，安装过程参考[docs](https://docs.microsoft.com/en-us/windows/wsl/install). docker desktop安装过程参考[docs](https://docs.docker.com/docker-for-windows/install).

### 第二步: 创建工程文件或者在已有的工程下构建dockerfile
方法一：创建dockerfile是为了将工程构建为镜像文件

在实际工程里，直接根据第三方库的dockerfile构建就行了
```shell
# 使用dockerfile进行创建镜像文件:

# A dockerfile must always start by importing the base image.
# We use the keyword 'FROM' to do that.
# In our example, we want import the python image.
# So we write 'python' for the image name and 'latest' for the version.
FROM python:latest

# In order to launch our python code, we must import it into our image.
# We use the keyword 'COPY' to do that.
# The first parameter 'main.py' is the name of the file on the host.
# The second parameter '/' is the path where to put the file on the image.
# Here we put the file at the image root folder.
COPY main.py /

# We need to define the command to launch when we are going to run the image.
# We use the keyword 'CMD' to do that.
# The following command will execute "python ./main.py".
CMD [ "python", "./main.py" ]

```

在dockerfile同目录下运行下面命令行：
```shell
# 1. 构建（必要）
docker build -t imagename:tag .
# 运行(可选)
docker run imagename:tag
# 运行进入容器查看环境库是否正确等
docker run -it --name test imagename:tag
```

方法二： 使用 命令 `docker commit` 进行手动指定 
1. 拉取基本镜像[菜鸟教程](https://www.runoob.com/docker/docker-container-usage.html), [官方文档](https://docs.docker.com/engine/reference/commandline/commit/)
```shell
# dockerhub 拉取基本镜像
# 以Ubuntu的镜像为例子
docker pull ubuntu  
```
2. 运行容器并查询当前运行容器的id
```shell
# 用Ubuntu镜像传入 /bin/bash 参数创建并运行一个容器
docker run -i ubuntu /bin/bash

# 查询当前运行容器的id
docker ps -a

```
3. 在运行中的容器内进行一些操作,安装依赖库等，配置环境
```shell
git clone **.git
pip install numpy
...
```
4. 对运行中的容器查询到id，imagename后通过 docker commit命令进行创建镜像
```shell
docker commit container_id image_name:image_tag
```
5. 查询创建的镜像
```shell
docker image ls
```
6. 停止/重启/删除容器运行
```shell
docker stop container_id # 停止

docker restart container_id # 重启

docker kill container_id # kill

docker rm container_id # 删除
```

### 第三步: 上传镜像文件到dockerhub或者公司库中
- 传到自建(公司)第三方库
```shell
# 1. docker默认安全地址为dockhub，因此自建库需要在 docker desktop的setting里的docker engine添加“insecure-registries"如下：

{
  "builder": {
    "gc": {
      "defaultKeepStorage": "20GB",
      "enabled": true
    }
  },
  "experimental": false,
  "features": {
    "buildkit": true
  },
  "registry-mirrors": [],
  "insecure-registries": [
    "harbor.smoa.cc"
  ]
}

# 2. 重命名镜像并且推送到自建库
docker login https://harbor.smoa.cc
docker tag myimage:tag harbor.smoa.cc/myimage:tag
docker push harbor.smoa.cc/myimage:tag
```
<font color= #DCDCDC>5000端口是镜像端口，22是数据节点端口</font>

- 传到dockerhub库
参考[blog](https://www.jianshu.com/p/8c1e1789b358)
```shell
# 1. 重新命名-- 必须的步骤，不然会报错
docker tag imagename:tag docker_username/imagename:tag
# 2. 传送到dockerhub
docker push username/imagename:tag
```

### 第四步: 在集群里使用镜像创建容器跑程序
- [cmder](https://cmder.net/) & [schedctl](http://artifactory.sm/artifactory/schedctl/) 安装
```shell
# windows下 schedctl.exe 放在cmder文件夹下
or
# chmod +x 加执行操作
$ chmod +x ./schedctl
```
- 登录集群，输入分配的username和password
```shell
schedctl user login
```
- 创建Job来运行镜像
```shell
schedctl create --name JOBNAME --image IMAGENAME --cmd COMMAND [--gpu GPUCOUNT] [--ports PORT1,PORT2,PORT3... ] [--node NODENAME]

schedctl create --name wfsfaa --image "10.81.138.201:5000/tairuqiu/wfsfaa_ocr:2.0.2" --gpu 1 --cmd "cd /mnt/yfs/yifengxie/code/wfsfaa-ocr-server; export PY THONPATH=/mnt/yfs/yifengxie/code/wfsfaa-ocr-server:${PYTHONPATH}; python demo/demo_ocr_system.py" --node node19
```
分为debug和running两种模式，更多细节(XXXX)

### 第五步: pycharm/VS连接远程服务器到集群
同步文件更新

## Docker的一些命令和注意事项
### 基本命令
镜像(images)相关
```shell
dock# 所涉及的image_name 为repositoey
# 列出所有镜像
docker images

# 删除镜像
docker rmi image_name:image_tag


# 镜像重命名
docker tag src_image_name:src_image_tag des_image_name:des_image_tag

# 上传/下载 镜像
docker push image_name:image_name
docker pull image_name:image_name
```
容器(container)相关
```shell
# 列出所有容器
docker ps -a

# 列出最近创建的容器
docker ps -l

# 创建容器
docker run -i -t --name container_name images_name:image_tag /bin/bash

# 停止/启动/删除
docker stop container_name
docker start -i container_name
docker rm container_name

# 进入容器
docker attach container_id/container_name

# 从容器拷贝到本地 / 从本地拷贝到容器内
docker cp container:src_path local_path
docker cp local_path container:des_path

# 容器重命名
docker rename src_contrainer_name des_container_name
```
集群(cluster)相关
```shell
# 登录
schedctl user login

# 查看集群状态
schectl node list
# 查看指定节点的详细信息
schedctl node describe JOBNAME

# 创建job运行任务
schedctl create --name JOBNAME --image IMAGENAME --cmd COMMAND [--gpu GPUCOUNT] [--ports PORT1,PORT2,PORT3... ] [--node NODENAME]

# 查看job日志
schedctl log jobname
# 显示job最近的100条日志
schedctl log jobname -t 100

# 保存日志
schedctl log jobname > log.txt

# 查看job信息
schedctl describe jobname

# 查看所有job状态
schedctl list

# 删除job
schedctl delete jobname

# 查看节点中人员信息
schedctl.exe list all | grep node02
```
docker跑tensorboard
```shell
docker run -it -v /d/RepDistiller/save/student_tensorboards/S-resnet8x4_T-resnet32x4_cifar100_kd_r-0.1_a-0.9_b-0.0 _1:/workspace/showtmp  -p 16006:6006 --name aaaaa 10.81.138.201:5000/torchdistill:torch180cuda11v2.0
```
### 关于win10下修改docker镜像存储位置-通过软连接（MKLINK）修改
方法一: 更改Docker Desktop设定
1. 右键docker desktop图标点击Settings
2. 点击左侧menu，找到Resources按钮，点击并找到Disk Image Location点击browse并且改变Location
3. 点击apply and restart
使用该方法的同学可能会遇到根本找不到这个setting的情况，因为不同的版本该设置选项会出现在不同的地方，大家可以多找找。但是也有可能根本没有这个选项，我就是遇到这个情况，请大家尝试下一个方法(版本问题)

方法二: 创建软连接
1. 启动docker下查看docker的存储位置
```shell
docker info
```
查看`Docker Root Dir`对应的列表, 但是并不能看到在win下的路径
2. 通过Everything或者资源管理器，查看`.vhdx`结尾的文件所在位置，这些就是docker镜像路径
3. 将找到的`data/ext4.vhdx`，复制到如D盘下, 得到`D:/docker/data/ext4.vhdx`
4. 删除源文件，本文对应的是`data/ext4.vhdx`
5. 以管理员运行cmd并使用`mklink`命令创建软连接
```shell
# 创建软连接
mklink /J C:\Users\wzq\AppData\Local\Docker\wsl\data D:\docker\data

# 如果之后需要删除软连接
rmdir C:\Users\wzq\AppData\Local\Docker\wsl\data
```
6. 重新打开Docker Desktop, docker info 查看存储位置


### 注意事项
每次在Docker配置好后需要先commit, 然后push
dockerhub拉取python的时候, 搜索python进入官方库, 然后在最上方tags搜索版本
XXXX

### 关于集群架构和Docker踩坑记录
#### Docker概念
Docker包含两部分: 容器(Container)+镜像(images), 打个不恰当的彼方来说，容器相当于linux，镜像相当于anconda环境
- 镜像(images):
  在我的理解中，镜像包括基本镜像+最终镜像；
    
  基本镜像表示从DockerHub或者其他人的镜像中拉取到自己本地Docker的镜像；
    
  最终镜像表示在拉取成功之后，在容器中配置好相关的依赖项等操作之后重新commit操作后的镜像

- 容器(container):
  容器主要是为拉取的基本镜像所运行得到，主要目的为存放镜像并进行后续相关的配置

#### 集群概念
集群分两块：环境+数据；两块是分开放置的，用户本地无法访问环境文件

- 环境节点：
    保存了用户上传的images环境，可以看做是anaconda文件，且普通用户不可访问

- 数据节点：
    对用户可见，在 `**/data/home/username**` 文件夹下；在该目录下用户上传程序所需文件，且其余普通用户不可访问； 该节点每次启动时（即创建job时），需要指定image，以及模式(debug和run);
    img.jpg

## 集群Debug
### 集群debug
```shell
# 
schedctl create --name test --image "tensorflow/tensorflow:devel-gpu" --cmd "sleep 30m" --debug

schedctl exec test
```
注意事项：
- debug模式下不能指定gpu，否则会报websokcet的错误
- sleep 30m 不能写30 min
- sleep 3多个job命令用 分号(；)隔开0m 不能写30 min
- 在cmd里如果不加入 “sleep 2h”这个shell命令，debug进去会显示找不到容器

### 本地debug
1. 本地docker容器开启ssh server, 参考[docs](https://www.zymblog.top/blog/235)
```shell
# 开启容器
docker run -it ubuntu:18.04 /bin/bash
# 更新源
apt-get update
apt-get install vim
# 安装ssh服务
apt-get install openssh-server
# 创建/var/run/sshd文件夹
mkdir -p /var/run/sshd
# 启动ssh服务
/usr/sbin/sshd -D &
# 查看网络状态，看看22端口是否处于监听的状态
apt-get install net-tools
netstat -tunlp

Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      3862/sshd           
tcp6       0      0 :::22                   :::*                    LISTEN      3862/sshd
# 取消pam登录限制
sed -ri 's/session required pam-loginuid.so/#session required pam_loginuid.so/g' /etc/pam.d/sshd
# 修改sshd的设置，可以让容器使用ssh登录root账号
vim /etc/ssh/sshd_config
将PermitRootLogin的值从withoutPassword改为yes
# 创建.ssh目录，并创建authorized_keys文件
mkdir -p /root/.ssh
touch /root/.ssh/authorized_keys
# 添加自启动SSH服务的脚本run.sh
touch /run.sh
vim /run.sh
# run.sh内容是：
#!/bin/bash
/usr/sbin/sshd -D
# run.sh添加执行权限
chmod +x /run.sh
# 修改root密码
passwd 

Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully
root@1f250848d3c5:/#
```
2. add sudo username(集群的username), 参考[docs](https://blog.csdn.net/bryanwang_3099/article/details/109787215)
```shell
apt-get update -y && apt-get clean all

apt-get install -y sudo

echo "%sudo ALL=(ALL:ALL) ALL" >> /etc/sudoers

echo "%sudo ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

adduser yifengxie

vim /etc/sudoers

# 在/etc/sudoers文件中找到root	ALL=(ALL:ALL) ALL，在该行下面添加：
yifengxie	ALL=(ALL:ALL) ALL

su yifengxie # 切换用户
sudo ls # 使用sudo权限执行ls，只要不报错就证明设置成功。
```
3. 制作好的镜像push到集群(参考上述上传步骤)
4. 集群debug容器开启，注意  --ports指定只能在debug模式
```shell
schedctl create --name ssh51 --image "10.81.138.201:5000/imagename:tag" --gpu 1 --cmd "sleep 2h" --debug --ports 22
```
5. 进去容器开启ssh server, 参考[docs](https://blog.csdn.net/md521/article/details/52597398)
```shell
sudo apt-get update

sudo apt-get install openssh-server

sudo ps -e |grep ssh

sudo service ssh start

sudo ps -e |grep ssh
```
6. schedctl describe 查看job的远程debug节点`ip`以及`port`(默认会将22号端口映射为其他可用的，需要手动查看)
XXX.jpg
7. 上都完成后，就可以将集y群看做一般的远程服务器，用pycharm或者ssh连接（但是ssh试了不行，pycharm是可以的）
XXX.jpg

