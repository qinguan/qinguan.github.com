---
layout: post
title: Springboot获取内部属性值
---

Springboot获取内部属性值
========================
11 March 2018 - Beijing

为了检测线上服务的版本是否正确，需要通过一定的方式保留当前运行服务的版本信息。
springboot可以很方便的通过spring-boot-actuator来暴露各种endpoint信息，很适合服务的监控。

但有时候为了兼容已有的监控或者部署工具，需要定制一些方式来支持相关信息的暴露。如Jar内部属性文件，如HTTP接口，然后监控部署服务通过检查属性文件或者调用相关服务接口来确认当前服务部署版本是否正确无误的。

- 内部属性文件方式
    - 可以通过[git-commit-id-plugin](https://github.com/ktoso/maven-git-commit-id-plugin)生成git.properties文件等方式。参见[此文](http://qinguan.github.io/2018/03/11/Maven%E6%89%93%E5%8C%85%E4%BD%BF%E7%94%A8%E4%BB%A3%E7%A0%81%E7%89%88%E6%9C%AC%E5%8F%B7%E5%92%8C%E6%97%B6%E9%97%B4%E6%88%B3.html)
    - 或者通过[maven-resources-plugin](https://maven.apache.org/plugins/maven-resources-plugin/)给动态给属性文件中的属性赋值。下面的配置实例可以在编译的时候，将git.commit.id.abbrev和git.build.time分别赋值给info节点的git-version属性和app-build-time属性。

```Java
        #application.properties
        info.git-version=@git.commit.id.abbrev@
        info.app-build-time=@git.build.time@ 

        #pom.xml
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-resources-plugin</artifactId>
            <version>2.6</version>
            <configuration>
                <delimiters>
                    <delimiter>@</delimiter>
                </delimiters>
                <useDefaultDelimiters>false</useDefaultDelimiters>
            </configuration>
        </plugin>

        最终打包好的application.properties文件属性会变成如下:
        info.git-version=b0145c5
        info.app-build-time=20180309205538
```

- 接口方式
    - 通过接口等方式暴露出去，那么首先需要在服务运行时，能够拿到这些属性值。
    - 一种方式可以通过Java Properties来load属性文件，如下:

```Java
        private static Properties PROJECT_PROPERTIES = new Properties();
        PROJECT_PROPERTIES.load(BOOT.class.getClassLoader().getResourceAsStream("application.properties"));

        然后就能拿到相关的属性值:
        String version = PROJECT_PROPERTIES.getProperty("info.git-version");
        String buildTime = PROJECT_PROPERTIES.getProperty("info.app-build-time");
```

    - 另一种方式是实现Spring的 EnvironmentAware接口，想要暴露的属性在setEnvironment方法中进行赋值，如下：

```Java
        public class TestAbc implements EnvironmentAware {
            private String gitVersion;
            private String buildTime;
            private long startTime = 0;

            @Override
            public void setEnvironment(Environment environment) {
                gitVersion = environment.getProperty("info.git-version");
                buildTime = environment.getProperty("info.app-build-time");
                startTime = ManagementFactory.getRuntimeMXBean().getStartTime();
            }
```

