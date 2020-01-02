---
title: Учебник. Подготовка приложения Spring для развертывания в Azure Spring Cloud
description: В рамках этого учебника вы подготовите приложение Java Spring для развертывания.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e112fdc9e6f518e2ea3c72161e8978118cf19335
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890320"
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
2.0.x | Finchley.RELEASE | 2.0.x
2.1.x | Greenwich.RELEASE | 2.1.x

Включите один из представленных ниже фрагментов в `pom.xml`.  Выберите тот фрагмент кода, у которого версия совпадает с используемой вами.

### <a name="version-20x"></a>Версия 2.0.x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="version-21x"></a>Версия 2.1.x:
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
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

Дополнительные примеры доступны на GitHub: [Примеры для Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
