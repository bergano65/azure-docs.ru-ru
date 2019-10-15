---
title: Подготовка приложения Spring к развертыванию в Azure Spring Cloud | Документация Майкрософт
description: В рамках этого краткого руководства вы подготовите приложение Java Spring для развертывания.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: 98d9f3f656cff84cec8d223ed535255157155bd2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038333"
---
# <a name="tutorial-prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Руководство по подготовке приложения Java Spring для развертывания в Azure Spring Cloud

В этом кратком руководстве показано, как подготовить имеющееся приложение Java Spring Cloud для развертывания в Azure Spring Cloud.  При правильной конфигурации Azure Spring Cloud предоставляет надежные службы для мониторинга, масштабирования и обновления приложения Spring Cloud. 

## <a name="java-runtime-version"></a>Версия среды выполнения Java

В Azure Spring Cloud могут работать только приложения Spring/Java.

Поддерживаются Java 8 и Java 11. Среда размещения содержит последнюю версию Azul Zulu OpenJDK для Azure. Дополнительные сведения об Azul Zulu OpenJDK для Azure см. в [этой статье](https://docs.microsoft.com/azure/java/jdk/java-jdk-install). 

## <a name="spring-boot-and-spring-cloud-versions"></a>Версии Spring Boot и Spring Cloud

В Azure Spring Cloud поддерживаются только приложения Spring Boot. Поддерживаются Spring Boot 2.0 и 2.1. Поддерживаемые комбинации Spring Boot и Spring Cloud перечислены в таблице ниже.

Версия Spring Boot | Версия Spring Cloud
---|---
2.0.x | Finchley.RELEASE
2.1.x | Greenwich.RELEASE

Убедитесь, что файл `pom.xml` имеет зависимости Spring Boot и Spring Cloud согласно вашей версии.

### <a name="version-20"></a>Версия 2.0:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.9.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="version-21"></a>Версия 2.1:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR3</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Зависимость клиента Azure Spring Cloud

Azure Spring Cloud размещает компоненты Spring Cloud, такие как реестр служб Spring Cloud и сервер конфигурации Spring Cloud, и управляет ими. Включите клиентскую библиотеку Azure Spring Cloud в зависимости, чтобы разрешить обмен данными с экземпляром службы Azure Spring Cloud.

В таблице ниже перечислены правильные версии приложения Spring Boot/Spring Cloud.

Версия Spring Boot | Версия Spring Cloud | Версия Azure Spring Cloud
---|---|---
2.0.x | Finchley.RELEASE | 2.0.0-SNAPSHOT
2.1.x | Greenwich.RELEASE | 2.1.0-SNAPSHOT

Добавьте этот фрагмент кода в файл `pom.xml` с правильной версией Azure Spring Cloud в раздел dependency:

```xml
    <repositories>
        <repository>
            <id>nexus-snapshots</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
    
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0-SNAPSHOT</version>
    </dependency>
```

## <a name="other-required-dependencies"></a>Другие необходимые зависимости

Чтобы включить встроенные функции Azure Spring Cloud, приложение должно включить следующие зависимости. Это обеспечит правильную настройку приложения с каждым компонентом.  

### <a name="service-registry"></a>Реестр служб

Чтобы использовать управляемую службу реестра служб Azure, добавьте `spring-cloud-starter-netflix-eureka-client` в `POM.xml`, как показано ниже.

Конечная точка сервера реестра служб будет автоматически внедрена в приложение в качестве переменных среды. Приложения смогут регистрироваться на сервере реестра служб и обнаруживать другие зависимые микрослужбы.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

### <a name="distributed-configuration"></a>Распределенная конфигурация

Чтобы включить распределенную конфигурацию, добавьте `spring-cloud-config-client` в раздел зависимостей `pom.xml`.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Не указывайте `spring.cloud.config.enabled=false` в конфигурации начальной загрузки, так как это приведет к прерыванию работы приложения с сервером конфигурации.

### <a name="metrics"></a>Метрики

Добавьте `spring-boot-starter-actuator` в раздел зависимостей файла pom.xml. Метрики периодически извлекаются из конечных точек JMX. Их можно отобразить с помощью портала Azure.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### <a name="distributed-tracing"></a>Распределенная трассировка

Добавьте `spring-cloud-starter-sleuth` и `spring-cloud-starter-zipkin` в раздел зависимостей файла pom.xml, как показано ниже. Кроме того, необходимо включить экземпляр Azure App Insights для работы с экземпляром службы Azure Spring Cloud. Дополнительные сведения о том, как включить Application Insights с помощью Azure Spring Cloud, см. [здесь](spring-cloud-tutorial-distributed-tracing.md)

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

## <a name="next-steps"></a>Дополнительная информация

В этом учебнике вы узнали, как настроить приложение Java Spring для развертывания в Azure Spring Cloud.  Чтобы узнать, как включить сервер конфигурации, перейдите к следующему учебнику.

> [!div class="nextstepaction"]
> [Узнайте, как настроить сервер конфигурации](spring-cloud-tutorial-config-server.md).

