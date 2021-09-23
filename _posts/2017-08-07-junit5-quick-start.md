---
title: Junit 5 Quick Start
date: 2017/08/07
tags: [Java,测试,JUnit]
categories: [Tech]
toc: true
description: Junit 5 Quick Start
---

## 目录

+ [Quick Start](#Quick-Start)
+ [Maven集成](#Maven集成)
+ [Gradle集成](#Gradle集成)

## Quick Start

### Maven依赖配置_摘录_

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>11</maven.compiler.source>
    <maven.compiler.target>11</maven.compiler.target>

    <!-- junit -->
    <org.junit5.platform.version>1.3.2</org.junit5.platform.version>
    <org.junit5.jupiter.version>5.3.2</org.junit5.jupiter.version>
    <org.junit5.vintage.version>5.3.2</org.junit5.vintage.version>
</properties>

<!-- junit5 & junit4 -->
<dependency>
    <groupId>org.junit.platform</groupId>
    <artifactId>junit-platform-launcher</artifactId>
    <version>${org.junit5.platform.version}</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.junit.platform</groupId>
    <artifactId>junit-platform-engine</artifactId>
    <version>${org.junit5.platform.version}</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>${org.junit5.jupiter.version}</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-engine</artifactId>
    <version>${org.junit5.jupiter.version}</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.junit.vintage</groupId>
    <artifactId>junit-vintage-engine</artifactId>
    <version>${org.junit5.vintage.version}</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-params</artifactId>
    <version>${org.junit5.jupiter.version}</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>${junit4.version}</version>
    <scope>test</scope>
</dependency>
```

### Gradle依赖配置_摘录_

```groovy
allprojects {
    apply plugin: "java"
    apply plugin: "maven"
    apply plugin: "idea"
    
    sourceCompatibility = 11
    targetCompatibility = 11
}
ext {
    /* test */
    junit5_platform_version = "1.2.0"
    junit5_jupiter_version = "5.2.0"
}

testCompile(
    "org.junit.jupiter:junit-jupiter-api:${junit5_jupiter_version}"
)

testRuntime(
    "org.junit.platform:junit-platform-launcher:${junit5_platform_version}",
    "org.junit.jupiter:junit-jupiter-engine:${junit5_jupiter_version}"
)
```

### 用例注解

```java
@Test          // 一个测试方法
@DisplayName  // 测试类或方法的显示名称
@BeforeEach   // 在单个测试方法运行之前执行的方法
@AfterEach    // 在单个测试方法运行之后执行的方法
@BeforeAll    // 在所有测试方法运行之前执行的方法(方法必须是静态\static修饰)
@AfterAll     // 在所有测试方法运行之后执行的方法(方法必须是静态\static修饰)
@Disabled     // 禁用测试类或方法
@Tag          // 为测试类或方法添加标签
@Nested       // 嵌套测试
@TestFactory  // 动态测试
```

### 用例

```java
/**
 * Test_LangString
 *
 * @author yakir <a href="yakirchen.com">yakir</a> on 07/08/2017 18:45.
 */
@DisplayName("Test_LangString")
class Test_LangString {

    private LangString ls;

    @BeforeAll
    static void beforeAll() {

        System.out.printf("在所有测试方法运行之前执行的方法\n");
    }

    @BeforeEach
    void beforeEach() {

        System.out.printf("在每个测试方法运行之前执行的方法\n");
        ls = new LangString();
    }

    @AfterEach
    void afterEach() {

        System.out.printf("在每个测试方法运行之后执行的方法\n");
    }

    @AfterAll
    static void afterAll() {

        System.out.printf("在所有测试方法运行之后执行的方法\n");
    }

    @Test
    @DisplayName("测试 string object")
    void test_stringObj() {

        ls.stringObj();
    }

    @Test
    @Disabled
    void donothing() {

        // do nothing ...
    }

    @Nested
    @DisplayName("Test_Inner 嵌套测试")
    class Test_Inner {

        @BeforeEach
        void beforeEach() {

            System.out.printf("嵌套测试每个case之前运行\n");
        }

        @Test
        void test_echo() {

            Echo.echoInfo("嵌套测试");
        }
    }
}
```

IDE运行输出

```bash
在所有测试方法运行之前执行的方法
在每个测试方法运行之前执行的方法
在每个测试方法运行之后执行的方法
8月 07, 2017 9:41:38 下午 com.yakirchen.common.Echo echoInfo
信息: 字符串

void test.Test_LangString.donothing() is @Disabled
在每个测试方法运行之前执行的方法
在每个测试方法运行之后执行的方法
嵌套测试每个case之前运行
8月 07, 2017 9:41:39 下午 com.yakirchen.common.Echo echoInfo
信息: 嵌套测试
在所有测试方法运行之后执行的方法
```

### Maven集成

添加依赖

```xml
<properties>
    <plugin.surefire.version>2.22.1</plugin.surefire.version>
</properties>

<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>${plugin.surefire.version}</version>
    <dependencies>
        <dependency>
            <groupId>org.junit.platform</groupId>
            <artifactId>junit-platform-surefire-provider</artifactId>
            <version>${org.junit5.platform.version}</version>
        </dependency>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-engine</artifactId>
            <version>${org.junit5.jupiter.version}</version>
        </dependency>
    </dependencies>
</plugin>
```

运行`mvn clean verify`输出：

```bash
-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running test.Test_LangString
在所有测试方法运行之前执行的方法
在每个测试方法运行之前执行的方法
在每个测试方法运行之后执行的方法
8月 07, 2017 9:47:25 下午 com.yakirchen.common.Echo echoInfo
信息: 字符串
在每个测试方法运行之前执行的方法
在每个测试方法运行之后执行的方法
嵌套测试每个case之前运行
8月 07, 2017 9:47:25 下午 com.yakirchen.common.Echo echoInfo
信息: 嵌套测试
在所有测试方法运行之后执行的方法
Tests run: 3, Failures: 0, Errors: 0, Skipped: 1, Time elapsed: 0.116 sec - in test.Test_LangString

Results :

Tests run: 3, Failures: 0, Errors: 0, Skipped: 1
```

### Gradle集成

```groovy
/* root */
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath "org.junit.platform:junit-platform-gradle-plugin:1.2.0"
    }
}

/* project */
apply plugin: "org.junit.platform.gradle.plugin"

junitPlatform {
    // platformVersion '1.0.3'
    filters {
        engines {
            // include 'junit-jupiter', 'junit-vintage'
            // exclude 'custom-engine'
        }
        tags {
            // include 'fast'
            // exclude 'slow'
        }
        // includeClassNamePattern '.*Test'
    }
    // enableStandardTestTask true
    // reportsDir file('build/test-results/junit-platform') // this is the default
    // logManager 'org.apache.logging.log4j.jul.LogManager'
}
```

运行`gradle clean junitPlatform`输出：

```bash
> Task :java-core:junitPlatformTest

Test run finished after 5178 ms
[         1 containers found      ]
[         0 containers skipped    ]
[         1 containers started    ]
[         0 containers aborted    ]
[         1 containers successful ]
[         0 containers failed     ]
[         0 tests found           ]
[         0 tests skipped         ]
[         0 tests started         ]
[         0 tests aborted         ]
[         0 tests successful      ]
[         0 tests failed          ]

BUILD SUCCESSFUL in 14s
5 actionable tasks: 4 executed, 1 up-to-date
```
