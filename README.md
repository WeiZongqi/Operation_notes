## Introduction
Linux 命令
```shell
# 查看所有用户对应的权限路径
$ cat /etc/passwd 

# ----------------------------------
# 实时监控
$ watch -n 1 nvidia-smi

# ----------------------------------
# 文件个数
# 统计当前目录下文件的个数（不包括目录）
$ ls -l | grep "^-" | wc -l

# 统计当前目录下文件的个数（包括子目录）
$ ls -lR | grep "^-" | wc -l

# 查看某目录下文件夹(目录)的个数（包括子目录）
$ ls -lR | grep "^d" | wc -l

# 查看文件多少行
$ wc -l datas.txt.dyinfo.txt

# 子文件大小
$ du -h --max-depth=1

# ----------------------------------
# 重命名
$ mv /a /b/c     # a文件夹移动到b下名字为c
$ mv a b        # 当前目录重命名
```

Http server服务 （http_server.sh）
```shell
f=$1
ip=`hostname -I| cut -d' ' -f1`
echo "http://${ip}:8001/${f}"
python -m http.server 8001   # 运行
```

tmux分屏终端
是一个终端复用器，可以启动一系列会话
```shell
$ tmux                         # 启动
$ exit / Ctrl + D                  # 退出
$ tmux new -s <name>            # 启动命名tmux
$ tmux detach                   # 会话分离
$ tmux kill-session -t <name>       # 会话结束
$ tmux switch -t <name>           # 会话切换
$ ctrl + b, % (shift + 5)              # 分屏
$ ctrl + b, left or right              # 切换回话
$ ctrl + b, 0/1/2/3                 # 切换
$ ctrl + d                        # 退出
$ ctrl + b, +[                     #翻页
#上翻页（pgup） 下翻页（pgdn） 停止（q）
```

tar/zip 压缩/解压
```shell
$ zip –q –r xahot.zip /home/wwwroot/xahot   #压缩
$ unzip -o -d /home/sunny myfile.zip    #把myfile.zip文件解压到 /home/sunny/
$ tar -xf all.tar                      # tar 解压
$ tar -zcvf test.tar test                # tar 压缩
```

Docker
```shell
# 参考
# https://www.jianshu.com/p/0baa53de436a
# https://segmentfault.com/a/1190000012063374

# docker 操作
$ docker ps -a  # 列出所有 docker
$ sudo docker attach 44fc0f0582d9   # 最后是 容器id

# 对镜像中操作后需要保存当前镜像，否则，下次进入就清零了
$ docker commit 3b1be8838a8a test-python3.8

# 启动保存的镜像
$ docker run -i -t test-python3.8 /bin/bash

# 强制删除images
$ docker rm -f <id>

#打包
$ docker container ls -a

CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
7691a814370e ubuntu:18.04 "/bin/bash" 36 hours ago Exited (0) 21

$ docker export 7691a814370e > ubuntu.tar

#导入
$ cat ubuntu.tar | docker import - test/ubuntu:v1.0
$ docker image ls
REPOSITORY TAG IMAGE ID CREATED VIRTUAL SIZE
test/ubuntu v1.0 9d37a6082e97 About a minute ago 171.3 MB

or

$ docker import http://example.com/exampleimage.tgz example/imagerepo
```

Git
```shell
# 上传到master
$ git init
$ git remote add origin <path.git>
$ git add .
$ git commit -m "[feat] add a new feature"
$ git push -u origin master

# descript
# [feat]: 新功能
# [fix]: bug 修复
# [docs]: 仅修改文档
# [refactor]: 代码重构

# 上传到branch分支
$ git clone <path.git>
$ git branch # 查看所有branch
$ git checkout -b feat/wzq # 新建feat/wzq 的branch
$ git checkout tags/v0.1.0 # 切花到对应的标签
$ git checkout tags/v0.1.0 #拉取所有子模块目录
$ cp -r xxx1 xxx2
$ git status # 查看改变的结果
$ git add .
$ git commit -m "[feat] xxx"
$ git push origin feat/wzq

## 拉取指定分支
$ git checkout -b dev origin/dev    # 切换到dev开发分支
$ git submodule update --init --recursive    # 将多个或指定(将“--recursive” 替换成所指定的submodule名称)的submodule更新到本地

# branch 操作
$ git branch                      # 查看本地分支
$ git branch -r                   # 查看远程分支
$ git branch [name]               # 创建本地分支：注意新分支创建后不会自动切换为当前分支
$ git checkout [name]             # 切换分支
$ git checkout -b [name]          # 创建新分支并立即切换到新分支
$ git branch -d [name]            # 删除分支: -d选项只能删除已经参与了合并的分支，
                                                # 未有合并的分支是无法删除的。
                                                # 如果想强制删除一个分支，可以使用-D选项
$ git merge [name]                # 合并分支: 将名称为[name]的分支与当前分支合并
$ git push origin [name]          # 创建远程分支(本地分支push到远程)
$ git push origin --delete [name] # 删除远程分支：

# Tag
$ git tag -a v1.1 -m 'v1.1' # 将当前版本添加v1.1的tag
$ git push origin v1.1

$ git tag -d <tagname> # 删除 tag
$ git push --delete origin <tagname> # 删除远程 tag

# log 查看分支信息
git log --oneline --graph --decorate
``` 

Conda 操作
```shell
$ conda env list #列出已有环境
$ conda env export > py37.yaml # 导出当前环境
$ conda env create -f py37.yaml # 用yaml文件安装环境

# 自己建立
$ conda create -n name python=3.7
$ conda activate name
$ conda deactivate
$ conda env remove -n name

# PyPI镜像使用
# 临时使用
$ pip install -i https://pypi.tuna.tsinghua.edu.cn/simple <some-package>
# txt文件安装
$ pip install -r requirements.txt

# 设为默认
# 升级到pip >= 10.0.0
$ python -m pip install --upgrade pip
$ pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
# 如果pip 默认源的网络连接较差，临时使用本镜像站来升级 pip
$ python -m pip install -i https://pypi.tuna.tsinghua.edu.cn/simple --upgrade pip


# conda 添加清华源
$ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
$ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
$ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
$ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
$ conda config --set show_channel_urls yes

```

python 包
```shell
# opencv安装
$ pip install opencv-python
$ pip install opencv-contrib-python

# kornia 0.5.8对应torch 1.7 以上对应1.8

# Pycocotools windows 安装
$ conda install -c conda-forge pycocotools
$ pip install pycocotools-windows

# from shapely.geometry import Polygon找不到指定模块
$ pip uninstall shapely
# 然后执行
$ conda install shapely

# CRF环境安装
# windows
https://blog.csdn.net/weixin_38899860/article/details/95320949

# ubuntu
$ pip install cython 
$ pip install git+https://github.com/lucasb-eyer/pydensecrf.git
```

软件
```shell
# 录屏软件ocam
https://gsf-fl.softonic.com/263/0e1/aba6f8ad9bc21bf4bc0e95c4dc13a9e8e0/oCam_v520.0.exeExpires=1662597752&Signature=3905c167ee2f7f91f617eca595c780682f40aa73&url=https://ocam.en.softonic.com&Filename=oCam_v520.0.exe
```

Python书写
```shell
# @statocmethod 标签，用于定义常用函数
@staticmethod
def find_max_region(mask_sel):
    pass

# _seg(self), 子功能
def _seg(self):
    pass
```
