# v2sub
linux 上面 v2ray 订阅管理工具, 支持获取 vmess 订阅

帮助自己在 linux 上面订阅 [JustMySocks](https://justmysocks.net/members/aff.php?aff=18111)

## 功能支持
```
订阅管理:
    -sub add {name} {url} 
        添加一个订阅，订阅节点自动增加到 ser list
    -sub update {name} 
        更新一个订阅
    -sub update all 
        更新全部订阅结果
    -sub remove {name} 
        删除一个订阅
    -sub list 
        查看当前所有订阅

节点查看:
    -ser list 
        查看所有节点
    -ser set {ser_id} 
        设置默认节点
    -ser setx 
        对节点进行 ping 测速，之后将默认节点设置为最快节点
    -ser setflush
        将当前选择的节点输出到 /etc/v2sub/config.json
    -ser speedtest
        使用 tcping 查看各个节点的连接速度
    
管理 (TODO)
    -conf sport {socket_port} 
        socket 端口号管理, 默认 1080
    -conf hport {http_port} 
        http 端口号管理， 默认1081
  
连接
    -conn start 
        启用 v2ray 连接 server
    -conn kill 
        停止 v2ray （kill 掉其他 v2sub 和 v2ray）

其他
    -v, --version
        查看版本号
    -h, --help
        查看帮助说明
```

## 使用说明
请确保已安装 v2ray 到 /usr/local/bin/v2ray (v2fly 安装脚本默认安装位置)

先使用 `go build v2sub.go` 编译得到运行文件 `./v2sub`

```shell
# 添加一个订阅, 添加后会自动获取 vmess 连接设置
./v2sub -sub add 订阅别名 http://xxx.xxx.xxxxx?xxx

# 查看当前已有订阅列表
./v2sub -sub list
#=======================================================
#name       url
#订阅别名    http://xxx.xxx.xxxxx?xxx
#=======================================================

# 查看当前可用的服务器节点, [0] 代表这是默认连接节点
./v2sub -ser list
#=======================================================
#ID   别名                                               地址                     端口       类型
#[0]  JMS@xxx.jamjams.net:11111                          xxx.xxx.xxx.xxx         52623      tcp
# 1   JMS@xxx.jamjams.net:11111                          xxx.xxx.xxx.xxx         52623      tcp
# 2   JMS@xxx.jamjams.net:11111                          xxx.xxx.xxx.xxx         52623      tcp
# 3   JMS@xxx.jamjams.net:11111                          xxx.xxx.xxx.xxx         52623      tcp
#=======================================================

# 设置默认连接节点
./v2sub -ser set 2
#=======================================================
#ID   别名                                               地址                     端口       类型
# 0   JMS@xxx.jamjams.net:11111                          xxx.xxx.xxx.xxx         52623      tcp
# 1   JMS@xxx.jamjams.net:11111                          xxx.xxx.xxx.xxx         52623      tcp
#[2]  JMS@xxx.jamjams.net:11111                          xxx.xxx.xxx.xxx         52623      tcp
# 3   JMS@xxx.jamjams.net:11111                          xxx.xxx.xxx.xxx         52623      tcp
#=======================================================

# 杀死当前正在运行的 v2ray 和 v2sub 程序
./v2sub -conn kill

# 调用 /usr/local/bin/v2ray 程序连接刚刚设置节点
./v2sub -conn start


```

## 配置文件生成
v2ray 的配置文件由模板 json 生成，可自己修改模板 json 来设置路由等信息

模板 json 在第一次运行之后会输出到 `/etc/v2sub/config_module.json`


## 定时订阅脚本

首先将 v2sub 移动到 /ush/bin

可用以下脚本做开机自启 / cron 定时任务

```shell
#!/bin/bash

# 更新全部订阅结果
v2sub -sub updateall

# 节点测速 + 设置最快节点
v2sub -ser setx

# 杀掉当前正在运行的 v2sub / v2ray
v2sub -conn kill

# 启动 v2ray 连接
v2sub -conn start

```