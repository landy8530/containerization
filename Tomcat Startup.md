### 运行tomcat命令

在 linux 下启动 tomcat 主要有三种方式可以使用

```shell
# 第一种方式，直接启动
# ./startup.sh

# 第二种方式，控制台动态输入
# ./catalina.sh run

# 第三种方式，作为服务启动
# nohup ./startup.sh &
复制代码
```

其中第一种方式 `./startup.sh` 源代码就是执行 `./catalina.sh start` ，跟 `./catalina.sh run` 一样，当客户端连接断开时，tomcat 服务也随即终止。

第二种方式的好处在于可以动态显示 tomcat 控制台的输出信息，包括 `log4j` 和 `sysout` 等信息。使用这种方式可以很方便的用于查看程序启动和运行时的状况，不用每次都打开 `catalina.out` 文件查看。

第三种方式是使用 `nohup` 命令后台启动 tomcat 服务， `nohup` 英文全称是 no hang up ，意为不挂起，用于在系统后台不挂断地运行命令，即使退出终端也不会影响程序的运行。

> nohup 命令，在默认情况下（非重定向时），会输出一个名叫 nohup.out 的文件到当前目录下，如果当前目录的 nohup.out 文件不可写，输出重定向到 $HOME/nohup.out 文件中。

### 其他常用的命令

```shell
# 关闭 tomcat 服务
# ./shutdown.sh

# 调试 tomcat
# ./catalina.sh debug

# 查看 tomcat 进程
# ps -ef | grep tomcat

# 查看日志
# tail -f catalina.out

# 清空日志
# echo -n '' catalina.out

# 查看 tomcat 状态
# systemctl status tomcat

# 开机启动 tomcat
# systemctl start tomcat

# 关闭开机启动 tomcat
# systemctl stop tomcat

# 重启 tomcat
# systemctl restart tomcat
复制代码
```

> systemctl 是 systemd 的主命令，用于管理系统。


# 1. using startup.sh to start the Tomcat in the background
# the stdout/stderr information will write to CATALINA_OUT file
# sh $CATALINA_HOME/bin/startup.sh

# 2. using catalina.sh <run> to start the Tomcat in the foreground
# this command starts the Tomcat in the foreground and displays the running logs in the same console you are currently in.
# The process is terminated by closing the window/terminal. Also, hitting Ctrl+C will terminate Tomcat.
# So unless you are in local or dev environments, need to be careful while using catalina command along with run option.
sh $CATALINA_HOME/bin/catalina.sh run 2>&1 |tee $CATALINA_BASE/logs/castro.log

# using catalina.sh <run> to start the Tomcat in the background
# catalina.sh <start>: starts the Tomcat in the background. You will have to use the tail command to see the logs as following:
# tail -f $CATALINA_HOME/logs/catalina.out
