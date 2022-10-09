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
