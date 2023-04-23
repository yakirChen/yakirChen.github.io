---

title: Tomcat Native
date: 2023/04/23
tags: [Tomcat]

---

## 前置

- [下载链接](https://tomcat.apache.org/download-native.cgi)
- 安装**apr** `brew install apr apr-util `


## 编译


### **Tomcat Native 1.2.x**

```shell
./configure --with-apr=${BREW_OPT}/apr \
            --with-java-home=/Library/Java/JavaVirtualMachines/jdk-1.8.jdk/Contents/Home \
            --with-ssl=/Users/yakir/local/brew/opt/openssl@1.1 \
            --prefix=/Users/yakir/local/tcnative


make && make install
```

### **Tomcat Native 2.0.x**


```shell
./configure --with-apr=${BREW_OPT}/apr \
            --with-java-home=/Library/Java/JavaVirtualMachines/jdk-11.jdk/Contents/Home \
            --with-ssl=/Users/yakir/local/brew/opt/openssl@3 \
            --prefix=/Users/yakir/local/tcnative


make && make install
```