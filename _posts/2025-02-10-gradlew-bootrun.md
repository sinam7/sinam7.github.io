---
layout: post
title: "./gradlew bootRun은 어떻게 동작하는가?"
date: 2025-02-10
categories: [Gradle, Spring Boot]
tags: [spring-boot, gradle, gradle-wrapper, bootrun, java]
description: "Spring Boot 애플리케이션을 실행할 때 자주 사용하는 './gradlew bootRun' 명령어의 동작 원리와 내부 실행 과정을 자세히 알아봅니다. Gradle Wrapper의 개념부터 bootRun 태스크의 커스터마이징까지 상세히 설명합니다."
keywords: [gradlew, bootrun, spring boot, gradle wrapper, gradle 태스크, spring boot 실행, gradle 빌드]
image:
  path: /assets/img/posts/2025-02-10-gradlew-bootrun/laptop.webp
  alt: "laptop with coffee"
---

<style>.highlight .code pre.highlighter-rouge{font-size:90% !important}code.highlighter-rouge{font-size:90% !important}</style>

# /gradlew bootRun은 어떻게 동작하는가?

Spring Boot 애플리케이션을 실행할 때 흔히 사용하는 명령어가 있습니다.

```bash
./gradlew bootRun
```

Gradle을 사용한 Spring Boot 프로젝트라면 익숙한 명령어지만, 정확히 어떻게 동작하는지 알고 있나요? 이번 글에서는 `./gradlew bootRun`이 내부적으로 어떤 과정을 거치는지 알아보겠습니다.

---

## 1. `./gradlew`란?

먼저, `./gradlew`는 **Gradle Wrapper**의 실행 파일입니다. Gradle Wrapper를 사용하면 프로젝트마다 독립적인 Gradle 버전을 관리할 수 있으며, Gradle이 설치되지 않은 환경에서도 자동으로 다운로드하여 실행할 수 있습니다.

- `gradle` vs `gradlew` 차이점
  - `gradle` : 시스템에 설치된 Gradle을 사용
  - `gradlew` : 프로젝트에 설정된 Wrapper를 통해 특정 Gradle 버전 사용

즉, `./gradlew`는 프로젝트의 `gradle/wrapper/gradle-wrapper.properties`에 정의된 버전의 Gradle을 실행하는 역할을 합니다.

## 2. `bootRun` 태스크의 역할

Spring Boot 애플리케이션을 실행하는 역할을 하는 `bootRun`은 Gradle의 **JavaExec** 기반으로 동작하며, **Application Plugin**의 `run` 태스크와 개념적으로 유사합니다. `bootRun`이 실행되면 다음과 같은 과정이 이루어집니다.

1. **소스 코드 컴파일**: `compileJava` 및 `processResources` 태스크 실행
2. **클래스 파일 로딩**: `build/classes/java/main/` 경로의 클래스 파일을 JVM이 로드
3. **애플리케이션 실행**: `SpringApplication.run()`이 포함된 `main` 메서드 실행

Gradle 내부적으로는 `org.springframework.boot.gradle.tasks.run.BootRun` 클래스를 사용하여 애플리케이션을 실행합니다.

## 3. `bootRun` 실행 과정 자세히 살펴보기

`./gradlew bootRun --info` 명령어를 실행하면 내부적으로 어떤 일이 일어나는지 확인할 수 있습니다.

```bash
$ ./gradlew bootRun --info
```

실행하면 다음과 같은 로그를 볼 수 있습니다.

```plaintext
Starting process 'command '/Library/Java/JavaVirtualMachines/jdk-17/bin/java''. Working directory: /Users/example/project Command: /Library/Java/JavaVirtualMachines/jdk-17/bin/java ...
Starting BootRun with arguments: []
Task :compileJava
Task :processResources
Task :classes
Task :bootRun
```

이를 통해 `bootRun`이 실행되기 전 `compileJava`, `processResources`, `classes` 등의 태스크가 실행됨을 알 수 있습니다.

![Gradle bootRun 실행 과정](../assets/img/posts/2025-02-10-gradlew-bootrun/flowchart.webp){: width="50%" height="50%"}


## 4. `bootRun` 실행 옵션 커스터마이징

`bootRun` 실행 시 다양한 옵션을 추가할 수 있습니다. `build.gradle.kts`에서 `bootRun` 설정을 변경해 보겠습니다.

```kotlin
// build.gradle.kts
import org.springframework.boot.gradle.tasks.run.BootRun

tasks.named<BootRun>("bootRun") {
    args = listOf("--server.port=8081", "--spring.profiles.active=dev")
    jvmArgs = listOf("-Xmx1024m")
}
```

위와 같이 설정하면 `./gradlew bootRun` 실행 시:

- `--server.port=8081`: 애플리케이션이 8081 포트에서 실행됨
- `--spring.profiles.active=dev`: `dev` 프로파일이 활성화됨
- `-Xmx1024m`: JVM의 최대 힙 메모리가 1024MB로 설정됨

이 외에도 `bootRun` 태스크를 활용하여 환경 변수 설정, 특정 클래스 로딩, 로깅 설정 등 다양한 커스터마이징이 가능합니다.

### `build.gradle` 파일 예시

Gradle Groovy DSL을 사용하는 경우 `build.gradle` 파일은 다음과 같이 작성할 수 있습니다.

```groovy
plugins {
    id 'org.springframework.boot' version '3.1.0'
    id 'io.spring.dependency-management' version '1.1.0'
    id 'java'
}

group = 'com.example'
version = '1.0.0'
sourceCompatibility = '17'

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('bootRun') {
    args = ['--server.port=8081', '--spring.profiles.active=dev']
    jvmArgs = ['-Xmx1024m']
}
```

---

## 5. `bootRun` vs `./gradlew build` vs `java -jar build/libs/app.jar` 

Spring Boot 애플리케이션을 실행하는 방법은 여러 가지가 있습니다.

| 실행 방식                          | 특징                        |
| ------------------------------ | ------------------------- |
| `./gradlew bootRun`     | 개발용 실행. Spring DevTools 등을 통해 소스 코드 변경 시 자동 반영 가능 |
| `./gradlew build`              | 애플리케이션을 빌드하여 `build/libs`에 JAR 파일 생성 |
| `java -jar build/libs/app.jar` | 운영 환경 실행, JAR 패키징 후 실행    |

즉, `bootRun`은 개발 중 빠르게 실행할 때 유용하고, `./gradlew build`는 JAR 파일을 생성하는 용도로 사용됩니다. `java -jar`는 이렇게 빌드된 JAR을 실행하는 방식입니다.

---

## 마무리

`./gradlew bootRun`은 단순히 애플리케이션을 실행하는 것 같지만, 내부적으로는 **Gradle Wrapper 실행 → `bootRun` 태스크 실행 → 소스 코드 컴파일 → JVM 실행** 등의 과정이 포함되어 있습니다.

실행 과정과 옵션을 잘 이해하고 있으면 개발 환경에서 더 효율적으로 Gradle 프로젝트를 실행할 수 있습니다.

## References

- [Gradle 공식 문서](https://docs.gradle.org)
- [Running your Application with Gradle :: Spring Boot](https://docs.spring.io/spring-boot/gradle-plugin/running.html)