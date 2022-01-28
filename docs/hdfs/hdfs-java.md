## 读写HDFS


### 引入Maven依赖

```xml
<properties>
        <java.version>1.8</java.version>
        <hadoop.version>2.10.1</hadoop.version>
        <log4j.version>2.14.0</log4j.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-common</artifactId>
            <version>${hadoop.version}</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>${hadoop.version}</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
            <version>${hadoop.version}</version>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
            <version>${log4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

```

### 列出目录文件

> 本例使用Spring Boot CommandLineRunner

```java
@Component
public class MyCommandRunner implements CommandLineRunner {


    @Override
    public void run(String... args) throws IOException, URISyntaxException {
        Configuration conf=new Configuration();
        conf.setBoolean("dfs.client.use.datanode.hostname", true);
        conf.setBoolean("dfs.datanode.use.datanode.hostname", true);
        System.setProperty("HADOOP_USER_NAME","root");
        conf.set("fs.defaultFS", "hdfs://192.168.10.16:9000");
        FileSystem fileSystem = FileSystem.get(conf);
        FileStatus[] fs = fileSystem.listStatus(new Path("/tmp"));
        Path[] listPath = FileUtil.stat2Paths(fs);
        System.out.println("-----");
        for(Path p : listPath)
            System.out.println(p);
        fileSystem.close();
    }
}
```

