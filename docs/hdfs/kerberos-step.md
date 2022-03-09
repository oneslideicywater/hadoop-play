# Hadoop配置Kerberos


## 配置文件

- core-site.xml

```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://node1.bigdata.com:9000</value>
    </property>
   
    <!--kerberos related settings-->

    <!--Enable RPC service-level authorization-->
    <property>
        <name>hadoop.security.authorization</name>
        <value>true</value>
    </property>

    <!-- Enable authentication by Kerberos-->
    <property>
        <name>hadoop.security.authentication</name>
        <value>kerberos</value>
    </property>
</configuration>
```

- hdfs-site.xml

```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    
    <!--namenode kerberos config-->

    <!--Enable HDFS block access tokens for secure operations.-->
    <property>
      <name>dfs.block.access.token.enable</name>
      <value>true</value>
    </property>

    <!--Kerberos keytab file for the NameNode--> 
    <property>
      <name>dfs.namenode.keytab.file</name>
      <value>/etc/security/keytab/merge.keytab</value>
    </property>
    <!--Kerberos principal name for the NameNode.-->
    <property>
      <name>dfs.namenode.kerberos.principal</name>
      <value>hdfs/_HOST@BIGDATA.COM</value>
    </property>

    <!--datanode kerberos config-->       
    <property>  
      <name>dfs.datanode.data.dir.perm</name>  
      <value>700</value>  
    </property>
       
    <property>
      <name>dfs.datanode.keytab.file</name>
      <value>/etc/security/keytab/merge.keytab</value>
    </property>
   
    <property>
      <name>dfs.datanode.kerberos.principal</name>
      <value>hdfs/_HOST@BIGDATA.COM</value>
   </property>

   <property>
     <name>dfs.data.transfer.protection</name>
     <value>authentication</value>
   </property> 

   <property>
     <name>dfs.http.policy</name>
     <value>HTTPS_ONLY</value>
   </property> 

</configuration>
```

- ssl-server.xml

```xml
<configuration>
    <property>
       <name>ssl.server.truststore.location</name>
       <value>/etc/https/truststore.jks</value>
       <description>Truststore to be used by NN and DN. Must be specified.</description>
    </property>

    <property>
        <name>ssl.server.truststore.password</name>
        <value>icywater</value>
        <description>Optional. Default value is ""</description>
    </property>

    <property>
        <name>ssl.server.truststore.type</name>
        <value>jks</value>
        <description>Optional. The keystore file format, default value is "jks".</description>
    </property>

    <property>
        <name>ssl.server.truststore.reload.interval</name>
        <value>10000</value>
        <description>Truststore reload check interval, in milliseconds.Default value is 10000 (10 seconds).</description>
    </property>

    <property>
        <name>ssl.server.keystore.location</name>
        <value>/etc/https/keystore.jks</value>
        <description>Keystore to be used by NN and DN. Must be specified.</description>
    </property>

    <property>
        <name>ssl.server.keystore.password</name>
        <value>icywater</value>
        <description>Must be specified.</description>
    </property>

    <property>
        <name>ssl.server.keystore.keypassword</name>
        <value></value>
        <description>Must be specified.</description>
    </property>

    <property>
        <name>ssl.server.keystore.type</name>
        <value>jks</value>
        <description>Optional. The keystore file format, default value is "jks".</description>
    </property>

    <property>
        <name>ssl.server.exclude.cipher.list</name>
        <value>TLS_ECDHE_RSA_WITH_RC4_128_SHA,SSL_DHE_RSA_EXPORT_WITH_DES40_CBC_SHA,
    SSL_RSA_WITH_DES_CBC_SHA,SSL_DHE_RSA_WITH_DES_CBC_SHA,
    SSL_RSA_EXPORT_WITH_RC4_40_MD5,SSL_RSA_EXPORT_WITH_DES40_CBC_SHA,
    SSL_RSA_WITH_RC4_128_MD5</value>
        <description>Optional. The weak security cipher suites that you want excluded from SSL communication.</description>
    </property>

</configuration>
```

- ssl-client.xml

```xml
<configuration>

<property>
  <name>ssl.client.truststore.location</name>
  <value>/etc/https/truststore.jks</value>
  <description>Truststore to be used by clients like distcp. Must be
  specified.
  </description>
</property>

<property>
  <name>ssl.client.truststore.password</name>
  <value>icywater</value>
  <description>Optional. Default value is "".
  </description>
</property>

<property>
  <name>ssl.client.truststore.type</name>
  <value>jks</value>
  <description>Optional. The keystore file format, default value is "jks".
  </description>
</property>

<property>
  <name>ssl.client.truststore.reload.interval</name>
  <value>10000</value>
  <description>Truststore reload check interval, in milliseconds.
  Default value is 10000 (10 seconds).
  </description>
</property>

<property>
  <name>ssl.client.keystore.location</name>
  <value>/etc/https/keystore.jks</value>
  <description>Keystore to be used by clients like distcp. Must be
  specified.
  </description>
</property>

<property>
  <name>ssl.client.keystore.password</name>
  <value>icywater</value>
  <description>Optional. Default value is "".
  </description>
</property>

<property>
  <name>ssl.client.keystore.keypassword</name>
  <value></value>
  <description>Optional. Default value is "".
  </description>
</property>

<property>
  <name>ssl.client.keystore.type</name>
  <value>jks</value>
  <description>Optional. The keystore file format, default value is "jks".
  </description>
</property>

</configuration>

```

- hadoop-env.sh

```bash
export HDFS_NAMENODE_USER=hdfs
export HDFS_SECONDARYNAMENODE_USER=hdfs
export HADOOP_HOME=/opt/hadoop
export HADOOP_SHELL_EXECNAME=root
export JAVA_HOME=/opt/jdk
```


## 步骤

> 步骤基于CentOS 7.8

### 1. 安装kerberos

```bash
yum install krb5-server krb5-libs krb5-workstation -y
```

### 2. 修改kerberos server配置文件

```bash
# cat /var/kerberos/krb5kdc/kdc.conf
[kdcdefaults]
 kdc_ports = 88
 kdc_tcp_ports = 88

[realms]

 BIGDATA.COM = {
  #master_key_type = aes256-cts
  acl_file = /var/kerberos/krb5kdc/kadm5.acl
  dict_file = /usr/share/dict/words
  admin_keytab = /var/kerberos/krb5kdc/kadm5.keytab
  supported_enctypes = aes128-cts:normal des3-hmac-sha1:normal arcfour-hmac:normal camellia256-cts:normal camellia128-cts:normal des-hmac-sha1:normal des-cbc-md5:normal des-cbc-crc:normal
 }
```

### 3. 修改`krb5.conf`

```bash
# cat /etc/krb5.conf

# Configuration snippets may be placed in this directory as well
includedir /etc/krb5.conf.d/

[logging]
 default = FILE:/var/log/krb5libs.log
 kdc = FILE:/var/log/krb5kdc.log
 admin_server = FILE:/var/log/kadmind.log

[libdefaults]
 dns_lookup_realm = false
 ticket_lifetime = 24h
 renew_lifetime = 7d
 forwardable = true
 rdns = false
 pkinit_anchors = FILE:/etc/pki/tls/certs/ca-bundle.crt
 default_realm = BIGDATA.COM
# default_ccache_name = KEYRING:persistent:%{uid}

[realms]
 BIGDATA.COM = {
  kdc = node1.bigdata.com
  admin_server = node1.bigdata.com
 }

[domain_realm]
 .bigdata.com = BIGDATA.COM
 bigdata.com = BIGDATA.COM
```

### 4. 修改权限配置文件

```bash
# cat /var/kerberos/krb5kdc/kadm5.acl
*/admin@BIGDATA.COM	*
```

### 5. 创建Kerberos Relam

```bash
$ kdb5_util create -s -r BIGDATA.COM
```

输入数据库密码：

```bash
Loading random data
Initializing database '/var/kerberos/krb5kdc/principal' for realm 'BIGDATA.COM',
master key name 'K/M@BIGDATA.COM'
You will be prompted for the database Master Password.
It is important that you NOT FORGET this password.
Enter KDC database master key: 
Re-enter KDC database master key to verify: 
```

### 6. 启动kerberos服务

```bash
systemctl enable kadmin && systemctl start kadmin
systemctl enable krb5kdc && systemctl start krb5kdc
```

### 7. 创建kerberos管理员

```bash
kadmin.local
addprinc admin/admin@BIGDATA.COM
```

### 8. 创建用户组和用户

对于每个主机：

```bash
groupadd hadoop

useradd -u 502 yarn -g hadoop
useradd -u 501 hdfs -g hadoop
```


### 9. 生成keytab文件

对于每个主机:

添加用户:

```bash
$ kadmin.local
#创建用户
addprinc -randkey yarn/node1.bigdata.com@BIGDATA.COM

addprinc -randkey hdfs/node1.bigdata.com@BIGDATA.COM

addprinc -randkey HTTP/node1.bigdata.com@BIGDATA.COM
```

生成keytab文件:

```bash
# 生成密钥文件
cd /etc/security/keytab
# 所有yarn用户生成至yarn.keytab
kadmin.local -q "xst -k yarn.keytab  yarn/node1.bigdata.com@BIGDATA.COM"
kadmin.local -q "xst -k yarn.keytab  yarn/node2.bigdata.com@BIGDATA.COM"
# ...

kadmin.local -q "xst -k HTTP.keytab  HTTP/node1.bigdata.com@BIGDATA.COM"
kadmin.local -q "xst -k HTTP.keytab  HTTP/node2.bigdata.com@BIGDATA.COM"

kadmin.local -q "xst -k hdfs.keytab  hdfs/node1.bigdata.com@BIGDATA.COM"
kadmin.local -q "xst -k hdfs.keytab  hdfs/node2.bigdata.com@BIGDATA.COM"

```

合并keytab文件

```bash
#合并成一个keytab文件，rkt表示展示,wkt表示写入
$ ktutil
ktutil: rkt hdfs.keytab
ktutil: rkt HTTP.keytab
ktutil: rkt yarn.keytab
ktutil: wkt merged.keytab
#查看
klist -ket  merged.keytab
```

修改keytab文件权限：

```bash
chown hdfs:hadoop hdfs.keytab 
```
### 9. 生成证书文件

> 把keystore和truststore堪称K-V数据库。keystore用于存储私钥，truststore用于存储可信任证书。alias就是Key,证书就是value

生成CA证书:

```bash
# /etc/https为共享盘
cd /etc/https
openssl req -new -x509 -keyout ca.key -out ca.crt -days 9999 -subj '/C=CN/ST=beijing/L=chaoyang/O=lecloud/OU=dt/CN=bigdata.com'
```
在每一条机器上生成, keystore & trustore

```bash
# 生成 keystore,使用nodex.bigdata.com代替${fqdn}
keytool -keystore keystore -alias node1 -validity 9999 -genkey -keyalg RSA -keysize 2048 -dname "CN=${fqdn}, OU=DT, O=DT, L=CY, ST=BJ, C=CN"
 
 
# 添加 CA 到 truststore
keytool -keystore truststore -alias CARoot -import -file /etc/https/ca.crt
 
# 从 keystore 中导出 cert
keytool -certreq -alias node1 -keystore keystore -file node1.crt
 
# 用 CA 对 cert 签名
openssl x509 -req -CA /etc/https/ca.crt -CAkey /etc/https/ca.key -in node1.crt -out node1-signed.crt -days 9999 -CAcreateserial
 
# 将 CA 的 cert 和用 CA 签名之后的 cert 导入 keystore
keytool -keystore keystore -alias CARoot -import -file /etc/https/ca.crt
keytool -keystore keystore -alias node1 -import -file node1-signed.crt
```

保存keystore和truststore文件为`.jks`文件。

```bash
cp keystore /etc/https/keystore.jks
cp truststore /etc/https/truststore.jks
```

参考: [hadoop https配置](https://www.cnblogs.com/kisf/p/7573561.html)


### 10. 启动服务

启动namenode:

```bash
chown -R hdfs:hadoop hadoop
bin/hdfs namenode -format bigdata
bin/hdfs --daemon start namenode
bin/hdfs --daemon start datanode
```
检查进程启动情况：

```bash
[root@bogon hadoop]# jps
11361 NameNode
11626 DataNode
12508 Jps
```

### 11. 验证配置是否成功

登录kerberos

```bash
kinit -kt /etc/security/keytab/merge.keytab hdfs/node1.bigdata.com@BIGDATA.COM
```

列出文件系统的内容:

```bash
[root@bogon hadoop]# bin/hadoop dfs -ls /
WARNING: Use of this script to execute dfs is deprecated.
WARNING: Attempting to execute replacement "hdfs dfs" instead.

Found 1 items
drwxr-xr-x   - hdfs supergroup          0 2022-03-09 01:12 /tmp
```
## Reference List

1. [Hadoop in Secure Mode](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/SecureMode.html)