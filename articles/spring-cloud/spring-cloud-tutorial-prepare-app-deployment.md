---
title: Учебник. Подготовка приложения Spring для развертывания в Azure Spring Cloud
description: В рамках этого учебника вы подготовите приложение Java Spring для развертывания.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 6e35430713a3dbc8317944fed1180432a2083676
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461599"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>подготовке приложения Java Spring для развертывания в Azure Spring Cloud

В этом кратком руководстве показано, как подготовить имеющееся приложение Java Spring Cloud для развертывания в Azure Spring Cloud. При правильной настройке Azure Spring Cloud предоставляет надежные службы для мониторинга, масштабирования и обновления приложения Java Spring Cloud.

## <a name="java-runtime-version"></a>Версия среды выполнения Java

В Azure Spring Cloud могут работать только приложения Spring/Java.

Azure Spring Cloud поддерживает Java 8 и 11. Среда размещения содержит последнюю версию Azul Zulu OpenJDK для Azure. Дополнительные сведения об Azul Zulu OpenJDK для Azure см. в [статье об установке JDK](https://docs.microsoft.com/azure/java/jdk/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Версии Spring Boot и Spring Cloud

Azure Spring Cloud поддерживает только приложения Spring Boot. Она поддерживает Spring Boot версий 2.0 и 2.1. Поддерживаемые комбинации Spring Boot и Spring Cloud перечислены в таблице ниже.

Версия Spring Boot | Версия Spring Cloud
---|---
2.0 | Finchley.RELEASE
2.1 | Greenwich.RELEASE

Убедитесь, что файл pom.xml содержит правильные зависимости Spring Boot и Spring Cloud для вашей версии Spring Boot.

### <a name="dependencies-for-spring-boot-version-20"></a>Зависимости для Spring Boot версии 2.0

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

### <a name="dependencies-for-spring-boot-version-21"></a>Зависимости для Spring Boot версии 2.1

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

Azure Spring Cloud размещает компоненты Spring Cloud и управляет ими от вашего имени. Сюда относятся реестр облачной службы Spring Cloud и сервер конфигурации Spring Cloud. Включите в зависимости клиентскую библиотеку Azure Spring Cloud, чтобы разрешить обмен данными с экземпляром службы Azure Spring Cloud.

В следующей таблице перечислены нужные версии Azure Spring Cloud для приложения, в котором используются Spring Boot и Spring Cloud.

Версия Spring Boot | Версия Spring Cloud | Версия Azure Spring Cloud
---|---|---
2.0 | Finchley.RELEASE | 2.0
2.1 | Greenwich.RELEASE | 2.1

Добавьте в файл pom.xml одну из следующих зависимостей. Выберите ту зависимость, у которой версия Azure Spring Cloud совпадает с используемой в вашей среде.

### <a name="dependency-for-azure-spring-cloud-version-20"></a>Зависимость для Azure Spring Cloud версии 2.0

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Зависимость для Azure Spring Cloud версии 2.1

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Другие необходимые зависимости

Чтобы включить встроенные функции Azure Spring Cloud, приложение должно включить следующие зависимости. Это включение обеспечит правильную настройку приложения с каждым компонентом.  

### <a name="service-registry-dependency"></a>Зависимость реестра служб

Чтобы использовать управляемую службу реестра служб Azure, добавьте в файл pom.xml зависимость `spring-cloud-starter-netflix-eureka-client`, как показано ниже.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Конечная точка сервера реестра служб будет автоматически внедрена в приложение в виде переменных среды. Приложения смогут зарегистрироваться на сервере реестра служб и обнаруживать другие зависимые микрослужбы.

### <a name="distributed-configuration-dependency"></a>Зависимость распределенной конфигурации

Чтобы включить распределенную конфигурацию, добавьте зависимость `spring-cloud-config-client` в раздел зависимостей файла pom.xml.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Не указывайте `spring.cloud.config.enabled=false` в конфигурации начальной загрузки. В противном случае приложение не сможет взаимодействовать с сервером конфигурации.

### <a name="metrics-dependency"></a>Зависимость метрик

Добавьте зависимость `spring-boot-starter-actuator` в раздел зависимостей файла pom.xml, как показано ниже.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Метрики периодически извлекаются из конечных точек JMX. Эти метрики можно визуализировать с помощью портала Azure.

### <a name="distributed-tracing-dependency"></a>Зависимость распределенной трассировки

Добавьте зависимости `spring-cloud-starter-sleuth` и `spring-cloud-starter-zipkin`, как указано ниже, в раздел зависимостей файла pom.xml.

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

 Также вам нужно включить экземпляр Azure Application Insights для работы с экземпляром службы Azure Spring Cloud. В [этом руководстве](spring-cloud-tutorial-distributed-tracing.md) вы узнаете, как использовать Application Insights в Azure Spring Cloud.

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы узнали, как настроить приложение Java Spring для развертывания в Azure Spring Cloud. Чтобы узнать, как настроить экземпляр сервера конфигурации, перейдите к следующему учебнику.

> [!div class="nextstepaction"]
> [Сведения о настройке экземпляра сервера конфигурации](spring-cloud-tutorial-config-server.md).

Дополнительные примеры доступны на GitHub: [Примеры для Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
