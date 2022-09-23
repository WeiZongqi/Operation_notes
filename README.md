## Introduction
#查看所有用户对应的权限路径
cat /etc/passwd 

#（http_server.sh）Http server服务 
'''console
f=$1
ip=`hostname -I| cut -d' ' -f1`
echo "http://${ip}:8001/${f}"
python -m http.server 8001   # 运行
'''
