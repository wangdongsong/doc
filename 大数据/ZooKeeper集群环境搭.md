# Zookeeper集群环境搭建  


标签（空格分隔）： 大数据  

------

## 环境说明  

* 版本：Zookeeper 3.4.10
* 主机：192.168.254.200、192.168.254.201、192.168.254.202
* 原则：应用程序、配置文件、日志文件、数据文件相互隔离，以便升级维护
* 目录结构：
> zookeeper  
> ---- app：应用程序  
> ---- conf：配置文件  
> ---- data：数据文件  
> ---- logs：日志文件  

  主路径：/home/hadoop/zookeeper  

## 配置文件  

### zookeeper-env.sh  

 **特别说明：** 三台主机配置相同  

根据zkEnv.sh的说明，编写自定义的环境变量文件，指定主目录、配置文件目录、日志文件目录、日志级别等，配置如下：  
```
#NAME=zookeeper
ZOODIR="/home/hadoop/zookeeper"
ZOOCFGDIR="$ZOODIR/conf"
#ZOOCFG="$ZOOCFGDIR/zoo.cfg"
ZOO_LOG_DIR="/home/hadoop/zookeeper/logs"
ZOO_LOG4J_PROP="INFO, ROLLINGFILE"
#OOPIDFILE="$ZOODIR/data/$NAME.pid"
CLASSPATH="$ZOOCFGDIR:$CLASSPATH"
```

### myid

**特别说明：** 每台主机各不相同，200为1，201为2，203为3

```
1
```

### log4j.properties

**特别说明：** 变更点如下，其它保持不变

log.dir由ZOO_LOG_DIR指定
root.logger由ZOO_LOG4J_PROP批定

```
zookeeper.root.logger=INFO, CONSOLE
zookeeper.console.threshold=INFO
zookeeper.log.dir=/home/hadoop/zookeeper/logs
zookeeper.log.file=zookeeper.log
zookeeper.log.threshold=DEBUG
zookeeper.tracelog.dir=.
zookeeper.tracelog.file=zookeeper_trace.log
```

### 其它说明

因zkServer.sh中需要指定conf目录，所以启动命令及顺序如下：

```
export ZOOCFGDIR=/home/hadoop/zookeeper/conf

./zkServer.sh start
```
