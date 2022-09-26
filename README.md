## Introduction
查看所有用户对应的权限路径
```shell
$ cat /etc/passwd 
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

重命名
```shell
$ mv /a /b/c     a文件夹移动到b下名字为c
$ mv a b        当前目录重命名
```

文件个数
```shell
统计当前目录下文件的个数（不包括目录）
$ ls -l | grep "^-" | wc -l

统计当前目录下文件的个数（包括子目录）
$ ls -lR| grep "^-" | wc -l

查看某目录下文件夹(目录)的个数（包括子目录）
$ ls -lR | grep "^d" | wc -l

查看文件多少行
$ wc -l datas.txt.dyinfo.txt
```

Git
```
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
$ cp -r xxx1 xxx2
$ git status # 查看改变的结果
$ git add .
$ git commit -m "[feat] xxx"
$ git push origin feat/wzq

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
```

Conda 操作
```shell
$ conda env list #列出已有环境
$ conda create -n name python=3.7
$ conda activate name
$ conda deactivate
$ conda remove -n name
```
