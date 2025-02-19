### 管理端问题

> 管理端部署完成后，访问Web管理页面始终无法打开

解决办法：

1、确认浏览器访问地址是 https://[server]:4433/web/，注意不可缺少“/web/”这个路径

2、确认管理端进程的运行情况和TCP/4433端口开放情况，如果不正常需要重启管理端进程

   ```
   # 检查./server的进程是否运行正常
   ps ax | grep ./server | grep -v grep
   
   # 检查TCP/4433端口是否正常开放
   ss -ntpl
   ```

3、检查管理端主机是否开启了防火墙，导致目前无法访问，必要情况，考虑关闭防火墙

 ```
  #centos7 检查防火墙状态
  systemctl status firewalld
  
  #centos7 检查防火墙开放端口
  firewall-cmd --list-ports
  ```

4、Linux环境使用date命令确认系统时间的准确

5、如果以上都没有问题，请将server和client日志提供给我们

   ```
   节点端日志在安装目录的logs文件夹内，文件名为client.log
   Linux管理端日志在/usr/share/hfish/log文件夹内，文件名为server.log
   Windows管理端日志在C:\Users\Public\hfish\log文件夹内，文件名为server.log
   ```


### 节点问题

> 节点状态为红色离线

解决办法：

1、检查节点到管理端的网络连通情况，以下是几种常见情况

   ```
   节点每60秒连接管理端的TCP/4433端口一次，180秒内连接不上即显示为离线。
   刚完成部署或网络不稳定的时候会出现显示为离线。
   通常情况，等待2~3分钟，如果节点恢复绿色在线，那蜜罐服务也会从绿色启用，变成绿色在线。
   ```


2、如果确认网络访问正常，节点在管理端上始终离线，需要检查节点上的进程运行情况。如果进程运行异常，需要杀死全部关联进程后，重启进程，并记录错误日志。

   ```
   # 检查./client的进程是否运行正常
   ps ax | grep -E 'services|./client' | grep -v grep		
   ```

3. 如果以上都没有问题，请将server和client日志提供给我们

   ```
   节点端日志在安装目录的logs目录内，文件名为client.log
   Linux管理端日志在/usr/share/hfish/log文件夹内，文件名为server.log
   Linux管理端日志在C:\Users\Public\hfish\log文件夹内，文件名为server.log
   ```

   

### 蜜罐服务问题

> 节点在线，部分蜜罐服务在线，部分蜜罐服务离线

解决办法：

1、确认蜜罐服务进程是否还在运行？

   ```
   # 检查service的进程是否运行正常，如果进程退出，建议查看service的日志
   ps ax | grep service | grep -v grep
   ```

2、确认是否端口冲突？

   ```
   这个问题常见默认22端口的SSH服务，刚启动client的时候服务在线，过了一会儿后服务离线。
   使用ss -ntpl命令检查该蜜罐服务的端口是否被占用？如果被占用，建议修改该业务的默认端口。
   
   Windows操作系统上，如果用户启用了tcp端口监听，大概率会发现TCP 135、139、445、3389端口冲突，
   这是用于Windows默认占用了这些端口，不建议在Windows上监听TCP 135、139、445、3389端口。
   ```

> 变更服务模板后，蜜罐新服务访问不到	

```
在HFish当前的产品结构中，管理端**永远不会**主动连接节点进行节点配置的变更。
管理端仅负责生成一个配置，等待节点每60秒尝试连接管理端拉取。

蜜罐服务被攻击的结果，会实时上报到管理端。
```

