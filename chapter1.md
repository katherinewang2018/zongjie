# First Chapter

使用

```
jfinal-undertow
```

开发

使用gradle管理项目

```
// https://mvnrepository.com/artifact/com.jfinal/jfinal-undertow
compile group: 'com.jfinal', name: 'jfinal-undertow', version: '1.4'
```

如果使用maven管理项目

```
<dependency>
<groupId>com.jfinal</groupId>
<artifactId>jfinal-undertow</artifactId>
<version>1.4</version>
</dependency>
```

在主配置类中

```
        public static void main(String[] args) {

        UndertowServer.create(SystemConfig.class)
                .addHotSwapClassPrefix("com.eshanren.").setResourcePath("E:\\testCode\\V3\\web")
                .start();

            }
```

或者

```
UndertowServer.create(JFinalClubConfig.class).
setResourcePath("E:\\testCode\\summary\\jfinal-club\\jfinal-club\\src\\main\\webapp").start();
```

接入物流信息查询的api接口实现

https://blog.csdn.net/qq\_36410795/article/details/72462143

