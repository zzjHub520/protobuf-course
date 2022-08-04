# 1.说明

本文介绍Protobuf生成Java代码的方法，
配置对应的[Maven](https://so.csdn.net/so/search?q=Maven&spm=1001.2101.3001.7020)插件，
把.proto文件生成Java代码。

# 2.插件配置

创建Maven工程[grpc](https://so.csdn.net/so/search?q=grpc&spm=1001.2101.3001.7020)-compile，
修改pom.[xml](https://so.csdn.net/so/search?q=xml&spm=1001.2101.3001.7020)，
引入生成代码需要的jar包依赖，
以及构建配置：

```xml
<dependencies>
    <dependency>
        <groupId>io.grpc</groupId>
        <artifactId>grpc-protobuf</artifactId>
        <version>1.30.2</version>
    </dependency>
    <dependency>
        <groupId>io.grpc</groupId>
        <artifactId>grpc-stub</artifactId>
        <version>1.30.2</version>
    </dependency>
</dependencies>
<build>
    <extensions>
        <extension>
            <groupId>kr.motd.maven</groupId>
            <artifactId>os-maven-plugin</artifactId>
            <version>1.6.2</version>
        </extension>
    </extensions>
    <plugins>
        <plugin>
            <groupId>org.xolstice.maven.plugins</groupId>
            <artifactId>protobuf-maven-plugin</artifactId>
            <version>0.6.1</version>
            <configuration>
                <protocArtifact>com.google.protobuf:protoc:3.12.0:exe:${os.detected.classifier}</protocArtifact>
                <pluginId>grpc-java</pluginId>
                <pluginArtifact>io.grpc:protoc-gen-grpc-java:1.32.1:exe:${os.detected.classifier}</pluginArtifact>
                <protoSourceRoot>src/main/proto</protoSourceRoot>
                <outputDirectory>src/main/java</outputDirectory>
                <clearOutputDirectory>false</clearOutputDirectory>
            </configuration>
            <executions>
                <execution>
                    <goals>
                        <goal>compile</goal>
                        <goal>compile-custom</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

# 3.创建.proto文件

新建src/main/proto目录，
在里面创建helloworld.proto文件：

```protobuf
// 显示声明使用proto3, 否则使用默认的proto2
syntax = "proto3";
 
// 生成类的包名
option java_package = "com.asiainfo.yuwen.grpc.helloworld";
// 生成类的文件名，否则默认生成的类名为proto文件名的驼峰命名
option java_outer_classname = "HelloWorldProto";
// 定义的所有消息、枚举和服务生成对应的多个类文件，而不是以内部类的形式出现
option java_multiple_files = false;
 
// greeting服务定义
service Greeter {
  // sayHello方法，格式为"方法名 请求参数 返回参数"
  rpc SayHello (HelloRequest) returns (HelloReply) {}
  // 另一个sayHello方法
  rpc SayHelloAgain (HelloRequest) returns (HelloReply) {}
}
 
// 方法请求,包含用户名
message HelloRequest {
  string name = 1;
}
 
// 方法响应,包含响应的消息
message HelloReply {
  string message = 1;
}
```

# 4.生成Java代码

在grpc-compile下执行Maven编译命令：

```cmd
mvn clean compile
```

编译成功日志：

```info
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] Detecting the operating system and CPU architecture
[INFO] ------------------------------------------------------------------------
[INFO] os.detected.name: windows
[INFO] os.detected.arch: x86_64
[INFO] os.detected.version: 10.0
[INFO] os.detected.version.major: 10
[INFO] os.detected.version.minor: 0
[INFO] os.detected.classifier: windows-x86_64
[INFO] 
[INFO] ------------------< com.asiainfo.yuwen:grpc-compile >-------------------
[INFO] Building grpc-compile 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ grpc-compile ---
[INFO] Deleting D:\Code\Work\Telemetry\grpc-demo\grpc-compile\target
[INFO] 
[INFO] --- protobuf-maven-plugin:0.6.1:compile (default) @ grpc-compile ---
[INFO] Compiling 1 proto file(s) to D:\Code\Work\Telemetry\grpc-demo\grpc-compile\src\main\java
[INFO] 
[INFO] --- protobuf-maven-plugin:0.6.1:compile-custom (default) @ grpc-compile ---
[INFO] Compiling 1 proto file(s) to D:\Code\Work\Telemetry\grpc-demo\grpc-compile\src\main\java
[INFO] 
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ grpc-compile ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory D:\Code\Work\Telemetry\grpc-demo\grpc-compile\src\main\resources
[INFO] Copying 1 resource
[INFO] Copying 1 resource
[INFO] 
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ grpc-compile ---
[INFO] Changes detected - recompiling the module!
[WARNING] File encoding has not been set, using platform encoding UTF-8, i.e. build is platform dependent!
[INFO] Compiling 2 source files to D:\Code\Work\Telemetry\grpc-demo\grpc-compile\target\classes
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  3.998 s
[INFO] Finished at: 2021-09-29T17:33:22+08:00
[INFO] ------------------------------------------------------------------------
```

会在src/main/java目录下生成代码:

![img](MarkDownImages/Protobuf%E7%94%9F%E6%88%90Java%E4%BB%A3%E7%A0%81(Maven).assets/06ccdc0dbf1992d4e4c98d9003cac75e.png)

# 5.插件参数说明

下面详细说明protobuf-maven-plugin插件配

configuration下的配置项：

1. protocArtifact：指定Protobuf编译器protoc具体版本，用于生成Java消息对象
2. pluginId：指定protoc的插件Id
3. pluginArtifact：指定生成Java代码的具体插件版本,用于生成Java接口服务
4. protoSourceRoot：proto文件所在目录
5. outputDirectory：Java类输出目录
6. clearOutputDirectory：是否需要清理输出目录

goals目标说明：

1. compile：编译消息对象
2. compile-custom :依赖上一步生成的消息对象,生成接口服务

说明一下，
上面clearOutputDirectory设置为false,
不清理输出目录，
是因为compile-custom时需要依赖上一步生成的消息对象，
如果清理了输出目录，
则compile-custom执行时会报错，
但是关闭了自动清理，
如果重新生成代码，
需要手动删除输出目录下的所有代码。

# 6.查看插件帮助手册

查看插件帮助手册(简略)
mvn help:describe -Dplugin=protobuf
查看插件帮助手册(详细，包含配置参数说明)
mvn help:describe -Dplugin=protobuf -Ddetail
查看插件帮助手册(详细，指定goal的说明)
mvn help:describe -Dplugin=protobuf -Dgoal=compile-custom -Ddetail

命令输出内容如下，
有部分省略：

```protobuf
Name: Maven Protocol Buffers Plugin
Description: Maven Plugin that executes the Protocol Buffers (protoc)
  compiler
Group Id: org.xolstice.maven.plugins
Artifact Id: protobuf-maven-plugin
Version: 0.6.1
Goal Prefix: protobuf
 
This plugin has 15 goals:
 
protobuf:compile
  Description: This mojo executes the protoc compiler for generating main
    Java sources from protocol buffer definitions. It also searches dependency
    artifacts for .proto files and includes them in the proto_path so that they
    can be referenced. Finally, it adds the .proto files to the project as
    resources so that they are included in the final artifact.
protobuf:compile-cpp
  Description: This mojo executes the protoc compiler for generating main C++
    sources from protocol buffer definitions. It also searches dependency
    artifacts for .proto files and includes them in the proto_path so that they
    can be referenced. Finally, it adds the .proto files to the project as
    resources so that they are included in the final artifact.
protobuf:compile-csharp
  Description: This mojo executes the protoc compiler for generating main C#
    sources from protocol buffer definitions. It also searches dependency
    artifacts for .proto files and includes them in the proto_path so that they
    can be referenced. Finally, it adds the .proto files to the project as
    resources so that they are included in the final artifact.
protobuf:compile-custom
  Description: This mojo executes the protoc compiler with the specified
    plugin executable to generate main sources from protocol buffer
    definitions. It also searches dependency artifacts for .proto files and
    includes them in the proto_path so that they can be referenced. Finally, it
    adds the .proto files to the project as resources so that they are included
    in the final artifact.
protobuf:help
  Description: Display help information on protobuf-maven-plugin.
    Call mvn protobuf:help -Ddetail=true -Dgoal=<goal-name> to display
    parameter details.
protobuf:test-compile
  Description: This mojo executes the protoc compiler for generating test
    Java sources from protocol buffer definitions. It also searches dependency
    artifacts in the test scope for .proto files and includes them in the
    proto_path so that they can be referenced. Finally, it adds the .proto
    files to the project as test resources so that they can be included in the
    test-jar artifact.
......
For more information, run 'mvn help:describe [...] -Ddetail'
```

