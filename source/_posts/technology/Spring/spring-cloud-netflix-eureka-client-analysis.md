---
title: Spring Cloud Netflix Eureka Client Analysis
date: 2019-08-16 16:00:00
toc: true
categories:
- Technology
- Spring
- Spring Cloud
tags:
- Netflix
- Eureka
---

![Spring Cloud Netflix Eureka Client Analysis](/images/test.jpg)

Spring Cloud Netflix Eureka Client Analysis

<!-- more -->

## 1. Dependencies Version

- **Spring Boot**
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.7.RELEASE</version>
    <relativePath/>
</parent>
```

- **Spring Cloud**
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Greenwich.SR2</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

[依赖顺序](#3-1-依赖顺序)

## 2. Clone Source Code(Optional)

### 2.1 [`spring-cloud-release`](https://github.com/spring-cloud/spring-cloud-release/tree/vGreenwich.SR2)

```sh
$ git clone -b vGreenwich.SR2 git@github.com:spring-cloud/spring-cloud-release.git
##! or
$ git clone -b vGreenwich.SR2 https://github.com/spring-cloud/spring-cloud-release.git

$ cd spring-cloud-release ; git checkout -b vGreenwich.SR2
```

### 2.2 [`spring-cloud-netflix`](https://github.com/spring-cloud/spring-cloud-netflix/tree/v2.1.2.RELEASE)

```sh
$ git clone -b v2.1.2.RELEASE git@github.com:spring-cloud/spring-cloud-netflix.git
##! or
$ git clone -b v2.1.2.RELEASE https://github.com/spring-cloud/spring-cloud-netflix.git

$ cd spring-cloud-netflix ; git checkout -b v2.1.2.RELEASE
```

## 3. Project Structure

- `spring-cloud-release`
	- `docs`: Spring Cloud Starter Docs
	- **`spring-cloud-dependencies`**: 管理依赖(**`*-dependencies`**)版本号.
	- `spring-cloud-starter-parent`: 依赖 **`spring-cloud-dependencies`**

	目标: **`org.springframework.cloud:spring-cloud-netflix-dependencies:2.1.2.RELEASE`**

- `spring-cloud-netflix`
	- **`spring-cloud-netflix-dependencies`**
	- `spring-cloud-netflix-archaius`
	- `spring-cloud-netflix-core`
	- `spring-cloud-netflix-concurrency-limits`
	- `spring-cloud-netflix-hystrix-dashboard`
	- `spring-cloud-netflix-hystrix-stream`
	- **`spring-cloud-netflix-eureka-client`**
	- `spring-cloud-netflix-eureka-server`
	- `spring-cloud-netflix-turbine`
	- `spring-cloud-netflix-turbine-stream`
	- `spring-cloud-netflix-sidecar`
	- `spring-cloud-netflix-zuul`
	- `spring-cloud-netflix-ribbon`
	- **`spring-cloud-starter-netflix`**
		- `spring-cloud-starter-netflix-archaius`
		- **`spring-cloud-starter-netflix-eureka-client`**
		- `spring-cloud-starter-netflix-eureka-server`
		- `spring-cloud-starter-netflix-hystrix`
		- `spring-cloud-starter-netflix-hystrix-dashboard`
		- `spring-cloud-starter-netflix-ribbon`
		- `spring-cloud-starter-netflix-turbine`
		- `spring-cloud-starter-netflix-turbine-stream`
		- `spring-cloud-starter-netflix-zuul`
	- `spring-cloud-netflix-hystrix`
	- `docs`

### 3.1 依赖顺序

**`spring-cloud-dependencies` --> `spring-cloud-netflix-dependencies` --> `spring-cloud-starter-netflix-eureka-client` --> `spring-cloud-netflix-eureka-client`**
