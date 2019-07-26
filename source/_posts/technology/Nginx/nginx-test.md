---
title: Nginx Test
date: 2019-06-18 16:00:00
toc: true
categories:
- Technology
- Nginx
tags:
- Nginx
---

![Nginx Deploy Test](https://www.nginx.com/wp-content/uploads/2019/05/NGINX-Conf-2019-nginx.com-hero-1054x644-1-min.png)

Nginx Deploy Test

<!-- more -->

> ECS for Aliyun, CentOS 7.2 64Bit

## Download

- Download
```sh
$ cd ~/Downloads

$ wget http://nginx.org/download/nginx-1.16.0.tar.gz
$ wget https://nodejs.org/dist/v10.16.0/node-v10.16.0-linux-x64.tar.xz
$ wget http://mirror.bit.edu.cn/apache/tomcat/tomcat-7/v7.0.94/bin/apache-tomcat-7.0.94.tar.gz
$ wget https://dev.tencent.com/u/THRAEX/p/Books/git/raw/master/jdk-8u211-linux-x64.tar.gz
```

- Extract
```sh
$ cd ~/Workspace/Kits

$ tar -xzf ~/Downloads/nginx-1.16.0.tar.gz
$ tar -xJf ~/Downloads/node-v10.16.0-linux-x64.tar.xz
$ tar -xzf ~/Downloads/apache-tomcat-7.0.94.tar.gz
$ tar -xzf ~/Downloads/jdk-8u211-linux-x64.tar.gz
```

- Rename
```sh
$ mv apache-tomcat-7.0.94/ tomcat7
$ mv nginx-1.16.0/ nginx
$ mv node-v10.16.0-linux-x64/ node
$ mv jdk1.8.0_211/ jdk1.8
```

## Install

- JDK/Tomcat7(Environment Variables)
```sh
$ vim ~/.bash_profile

# User specific environment and startup programs

#########################
PATH=$PATH:$HOME/bin

KITS_HOME=~/Workspace/Kits
export JAVA_HOME=$KITS_HOME/jdk1.8
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export CATALINA_BASE=$KITS_HOME/tomcat7
export CATALINA_HOME=$KITS_HOME/tomcat7
PATH=$PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin:$KITS_HOME/node/bin

export PATH
#########################

$ . ~/.bash_profile
$ which java startup.sh node
/root/Workspace/Kits/jdk1.8/bin/java
/root/Workspace/Kits/tomcat7/bin/startup.sh
/root/Workspace/Kits/node/bin/node

$ whereis java startup.sh node
java: /root/Workspace/Kits/jdk1.8/bin/java
startup: /root/Workspace/Kits/tomcat7/bin/startup.bat /root/Workspace/Kits/tomcat7/bin/startup.sh
node: /root/Workspace/Kits/node/bin/node

$ java -version ; version.sh ; node -v
```

- Nginx(make), http://nginx.org/en/docs/configure.html
```sh
$ cd ~/Workspace/Kits/nginx
$ ./configure
```
  - Questions:
    - `./configure: error: the HTTP rewrite module requires the PCRE library.`
    ```sh
    $ yum -y install pcre-devel
    ```
    - `./configure: error: the HTTP gzip module requires the zlib library.`
    ```sh
    $ yum install -y zlib-devel
  ```

```sh
$ ./configure
...
Configuration summary
  + using system PCRE library
  + OpenSSL library is not used
  + using system zlib library

  nginx path prefix: "/usr/local/nginx"
  nginx binary file: "/usr/local/nginx/sbin/nginx"
  nginx modules path: "/usr/local/nginx/modules"
  nginx configuration prefix: "/usr/local/nginx/conf"
  nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
  nginx pid file: "/usr/local/nginx/logs/nginx.pid"
  nginx error log file: "/usr/local/nginx/logs/error.log"
  nginx http access log file: "/usr/local/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp"

$ make
$ make install
```

> **Important**
>
> 安装完成后添加环境变量`/usr/local/nginx/sbin/`
```sh
$ vim ~/.bash_profile
PATH=$PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin:$KITS_HOME/node/bin:/usr/local/nginx/sbin/
```

- Git
```sh
$ yum install git
```

- Nmap
```sh
$ yum install nmap
```

## Run

### Tomcat7

- **Start**
1. `startup.sh`
```sh
$ startup.sh
Using CATALINA_BASE:   /root/Workspace/Kits/tomcat7
Using CATALINA_HOME:   /root/Workspace/Kits/tomcat7
Using CATALINA_TMPDIR: /root/Workspace/Kits/tomcat7/temp
Using JRE_HOME:        /root/Workspace/Kits/jdk1.8
Using CLASSPATH:       /root/Workspace/Kits/tomcat7/bin/bootstrap.jar:/root/Workspace/Kits/tomcat7/bin/tomcat-juli.jar
Tomcat started.
```
2. `catalina.sh run`(推荐)

> **Note**
>
> 本地自测: `curl http://localhost:8080`
>
> 公网访问去要配置 **安全组规则**
> 1. 点击 **`添加安全组规则`**
> 2. 端口范围：`8080/8080`
> 3. 授权对象：`0.0.0.0/0`

- **Stop**
1. `shutdown.sh`
```sh
$ shutdown.sh
Using CATALINA_BASE:   /root/Workspace/Kits/tomcat7
Using CATALINA_HOME:   /root/Workspace/Kits/tomcat7
Using CATALINA_TMPDIR: /root/Workspace/Kits/tomcat7/temp
Using JRE_HOME:        /root/Workspace/Kits/jdk1.8
Using CLASSPATH:       /root/Workspace/Kits/tomcat7/bin/bootstrap.jar:/root/Workspace/Kits/tomcat7/bin/tomcat-juli.jar
```
2. `catalina.sh stop`
3. `kill`
```sh
$ ps -ax | grep tomcat
$ kill <PID>
```

> **Question**
> 启动时卡在“ Deploying web application directory ”很久
>
> **Why**
> linux或者部分unix系统提供随机数设备是/dev/random 和/dev/urandom，其中urandom安全性没有random高，但random需要时间间隔生成随机数，jdk默认调用random，从而生成随机数时间间隔长从而到时Tomcat启动速度慢
>
> **Solution**
```
$ vim ~/Workspace/Kits/jdk1.8/jre/lib/security/java.security
...
# HANZO ADD
# securerandom.source=file:/dev/random
securerandom.source=file:/dev/./urandom
...
```

### Nginx

**需要将`/usr/local/nginx/sbin/`添加至环境变量**

- **Start**: `$ nginx`

> **Note**
>
> 本地自测: `curl http://localhost`
>
> 公网访问去要配置 **安全组规则**
> 1. 点击 **`添加安全组规则`**
> 2. 端口范围：`8080/8080`
> 3. 授权对象：`0.0.0.0/0`

- `nginx -s signal`
  - `stop` — fast shutdown
  - `quit` — graceful shutdown
  - `reload` — reloading the configuration file
  - `reopen` — reopening the log files

## Domain

添加 **解析记录**

- 记录类型: `A`
- 主机记录: `@` / `www`分别对应: http://hanzo.com.cn / http://www.hanzo.com.cn
- 记录值: **公网IP**

> **Note**
>
> 因备案原因, 主机记录使用`www`
