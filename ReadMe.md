## Docker_QuickStart      

agapple edited this page on 15 Nov 2018 · 1 revision
参考资料
QuickStart : https://github.com/alibaba/otter/wiki/QuickStart

###  Dockerfile   
Dockerfile文件：https://github.com/alibaba/otter/blob/master/docker/Dockerfile

####  注意点：

基于centos6.7最小镜像进行构建，安装一些必须的工具，比如tar/dstat/nc/man等，大概400MB
默认安装jdk 1.8、zookeeper、mysql，build.sh脚本里会自动下载jdk然后copy到docker里，大概500MB
自带日志清理脚本，会识别硬盘超过80%时，自动清理 因此，otter整个docker镜像在1GB左右，有一定的优化空间，比如使用jre、减少一些非必须的命令等
镜像内容
整个docker镜像

包含了otter所依赖的所有组件，比如mysql、zookeeper、manager、node，几个组件的互相依赖的配置都已经初始化完成.
整个镜像设计为单机的QuickStart启动模式，全部都是单点启动，并没有高可用的能力。如果需要高可用的组件，可以借助k8s做docker节点的高可用调度，或者参考Dockerfile按照业务需求改造为多docker协同部署的模式
内置的启动脚本：

```
echo "==> START ..."
start_mysql   # 启动mysql
start_zookeeper  # 启动zk
start_manager # 启动manager
start_node #启动node
echo "you can visit manager link : http://$host:8080/ , just have fun !"

echo "==> START SUCCESSFUL ..."

tail -f /dev/null &
# wait TERM signal
waitterm

echo "==> STOP"

stop_node
stop_manager
stop_zookeeper
stop_zookeeper
stop_mysql

echo "==> STOP SUCCESSFUL ..."
获取Docker
远程拉取

docker pull canal/otter-server
启动otter
curl -fsSL https://raw.githubusercontent.com/alibaba/otter/master/docker/run.sh | bash 
image.png | left | 747x224
```


注意点：

建议使用otter工程自带的run.sh脚本，会处理好host参数、端口映射、目录挂载等工作.
考虑otter docker自身会存储一些mysql、zookeeper的元数据，默认run.sh脚本会通过目录挂载的方式，将数据文件挂载到当前的data/目录下(包含zkData/mysql两个子目录)，所以执行curl之前最好进入到一个自己的工作目录，比如cd otter
image.png | left | 359x89

看到successful之后，就代表otter整体启动成功，就可以访问对应manager链接的地址，默认是 http://${host}:8080/
