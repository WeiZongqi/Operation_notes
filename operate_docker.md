# Operation docker

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

### 注意事项

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
    
