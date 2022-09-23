## Introduction
查看所有用户对应的权限路径
```
cat /etc/passwd 
```

Http server服务 （http_server.sh）
```
f=$1
ip=`hostname -I| cut -d' ' -f1`
echo "http://${ip}:8001/${f}"
python -m http.server 8001   # 运行
```

tmux分屏终端
是一个终端复用器，可以启动一系列会话
```
tmux                         # 启动
exit / Ctrl + D                  # 退出
tmux new -s <name>            # 启动命名tmux
tmux detach                   # 会话分离
tmux kill-session -t <name>       # 会话结束
tmux switch -t <name>           # 会话切换
ctrl + b, % (shift + 5)              # 分屏
ctrl + b, left or right              # 切换回话
ctrl + b, 0/1/2/3                 # 切换
ctrl + d                        # 退出
ctrl + b, +[                     #翻页
#上翻页（pgup） 下翻页（pgdn） 停止（q）
```

tar/zip 压缩/解压
```
zip –q –r xahot.zip /home/wwwroot/xahot   #压缩
unzip -o -d /home/sunny myfile.zip    #把myfile.zip文件解压到 /home/sunny/
tar -xf all.tar                      # tar 解压
tar -zcvf test.tar test                # tar 压缩
```

重命名
```
mv /a /b/c     a文件夹移动到b下名字为c
mv a b        当前目录重命名
```

文件个数
```
统计当前目录下文件的个数（不包括目录）
$ ls -l | grep "^-" | wc -l

统计当前目录下文件的个数（包括子目录）
$ ls -lR| grep "^-" | wc -l

查看某目录下文件夹(目录)的个数（包括子目录）
$ ls -lR | grep "^d" | wc -l

查看文件多少行
$ wc -l datas.txt.dyinfo.txt
```
