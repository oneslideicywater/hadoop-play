# Hadoop 运维



## namenode的文件分类

- fsimage
- edits
- seen_txid
- VERSION


### fsimage

fsimage文件是文件系统快照，可以使用`hdfs oiv`命令查看。


### edits

启动时会合并fsimage和edits文件的内容。edits文件是实时增加的操作日志，而fsimage时定时生成的。edits文件可以使用`hdfs oev`查看。


### seen_txid

edits文件的尾数,启动时会按照顺序加载edits文件，直至尾数指示的edits文件。



## Web Console CORS配置

界面上传文件，有的时候会提示有跨域问题上传不了。

- core-site.xml

```xml
        <!--web console cors settings-->
        <property>
            <name>hadoop.http.filter.initializers</name>
            <value>org.apache.hadoop.security.HttpCrossOriginFilterInitializer</value>
        </property>
        <property>
            <name>hadoop.http.cross-origin.enabled</name>
            <value>true</value>
        </property>
        <property>
            <name>hadoop.http.cross-origin.allowed-origins</name>
            <value>*</value>
        </property>
        <property>
            <name>hadoop.http.cross-origin.allowed-methods</name>
            <value>GET,POST,HEAD</value>
        </property>
        <property>
            <name>hadoop.http.cross-origin.allowed-headers</name>
            <value>X-Requested-With,Content-Type,Accept,Origin</value>
        </property>
        <property>
            <name>hadoop.http.cross-origin.max-age</name>
            <value>1800</value>
        </property>
```

参考：https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/HttpAuthentication.html


## 安全模式

HDFS启动时，会检测文件系统的完整性，此时不允许写操作，会处于所谓的“安全模式”（safe mode）。


查看安全模式状态：

```bash
hdfs dfsadmin -safemode get
```

强制退出安全模式：

```bash
hdfs dfsadmin -safemode leave
```


## Hadoop On Kubernetes

克隆本仓库，hadoop的编排文件在`kubernetes-hadoop`目录下。

### 部署步骤

#### 1. 部署StorageClass

本例使用的StorageClass，参考：https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner.git

#### 2. 加载两个镜像

```bash
hadoop:v3.3.1
spark:v3.0.3
```

#### 3. 编排文件

```bash
kubectl apply -f kubernetes-hadoop/*
```


### 部署模式

#### 云环境的差异

在容器环境和虚拟机部署Hadoop的不同：

1. 持久化卷 

合理挂载hadoop的临时目录，元数据目录等，使得Pod重启不会导致HDFS文件系统损坏。

2. 网络连通

由于Kubernetes会给Pod动态分配IP, 当Pod发生重启时，IP就会改变。

一般使用使用Headless Service和StatefulSet机制解决，这个机制可以使得`datanode pod`通过*DNS*直接访问`namenode pod`.

##### Pod使用主机网络

根据[hdfs读写模式](/hdfs/read-write-mode.md),客户端将数据直接写入datanode的RPC端口，因此Pod需要使用主机网络进行部署。

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: hdfs-datanode
  labels:
    app: hdfs-datanode
spec:
  selector:
    matchLabels:
      app: hdfs-datanode
  template:
    metadata:
      labels:
        app: hdfs-datanode
    spec:
      # 这里使用了主机网络
      hostNetwork: true
      hostPID: true
      dnsPolicy: ClusterFirstWithHostNet
```


Headless Service不会给Service分配ClusterIP, 因此解析Cluster Service时，DNS查询结果是Pod IP列表。




#### NameNode

NameNode Pod 定向绑定到一个节点，通过k8s node label

```bash
kubectl label node1 app=hdfs-namenode
```






## 使用Kerberos加固Hadoop 

配置Kerberos加固的Hadoop需要具备一些背景知识，包括:

- [Kerberos协议组件和认证流程](/hdfs/kerberos.md)
- HTTPS机制
- Java的TrustStore和Keystore的用途和区别 & keytool CLI使用



