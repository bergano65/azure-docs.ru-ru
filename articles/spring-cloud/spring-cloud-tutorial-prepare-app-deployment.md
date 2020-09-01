---
title: Как подготовить пружинное приложение Java для развертывания в Azure Веснного облака
description: Узнайте, как подготовить пружинное приложение Java для развертывания в Azure Веснного облака.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: ff797f8b6fd375a940f77b4e0400bcb7a74450c4
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179765"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>подготовке приложения Java Spring для развертывания в Azure Spring Cloud

В этой статье рассказывается, как подготовить имеющееся приложение Java Spring к развертыванию в Azure Spring Cloud. При правильной настройке Azure Spring Cloud предоставляет надежные службы для мониторинга, масштабирования и обновления приложения Java Spring Cloud.

Перед выполнением этого примера можно попробовать [базовое краткое руководство](spring-cloud-quickstart.md).

В других примерах объясняется, как развернуть приложение в Azure Spring Cloud, если настроен файл POM. 
* [Запуск первого приложения](spring-cloud-quickstart.md)
* [Создание и запуск микрослужб](spring-cloud-quickstart-sample-app-introduction.md)

В этой статье описываются необходимые зависимости и их добавление в файл POM.

## <a name="java-runtime-version"></a>Версия среды выполнения Java

В Azure Spring Cloud могут работать только приложения Spring/Java.

Azure Spring Cloud поддерживает Java 8 и 11. Среда размещения содержит последнюю версию Azul Zulu OpenJDK для Azure. Дополнительные сведения об Azul Zulu OpenJDK для Azure см. в [статье об установке JDK](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Версии Spring Boot и Spring Cloud

Чтобы подготовить существующее приложение Spring Boot к развертыванию в Azure Spring Cloud, включите в файл POM приложения зависимости Spring Boot и Spring Cloud, как описано в разделах ниже.

Azure Spring Cloud поддерживает только приложения Spring Boot версии 2.1 или 2.2. Поддерживаемые комбинации Spring Boot и Spring Cloud перечислены в таблице ниже.

Версия Spring Boot | Версия Spring Cloud
---|---
2.1 | Greenwich.RELEASE
2.2 | Hoxton.RELEASE
2.3 | Хокстон. SR5

### <a name="dependencies-for-spring-boot-version-21"></a>Зависимости для Spring Boot версии 2.1

Для Spring Boot версии 2.1 добавьте следующие зависимости в файл POM приложения.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Зависимости для Spring Boot версии 2.2

Для Spring Boot версии 2.2 добавьте следующие зависимости в файл POM приложения.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
### <a name="dependencies-for-spring-boot-version-23"></a>Зависимости для пружинной загрузки, версия 2,3

Для пружинной загрузки версии 2,3 Добавьте следующие зависимости в файл POM приложения.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.0.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR5</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
## <a name="azure-spring-cloud-client-dependency"></a>Зависимость клиента Azure Spring Cloud

Azure Spring Cloud размещает компоненты Spring Cloud и управляет ими от вашего имени. К ним относятся реестр облачной службы Spring Cloud и сервер конфигурации Spring Cloud. Включите в зависимости клиентскую библиотеку Azure Spring Cloud, чтобы разрешить обмен данными с экземпляром службы Azure Spring Cloud.

В следующей таблице перечислены нужные версии Azure Spring Cloud для приложения, в котором используются Spring Boot и Spring Cloud.

Версия Spring Boot | Версия Spring Cloud | Версия Azure Spring Cloud
---|---|---
2.1 | Greenwich.RELEASE | 2.1
2.2 | Hoxton.RELEASE | 2.2
2.3 | Хокстон. SR5 | 2.3

Добавьте в файл pom.xml одну из следующих зависимостей. Выберите ту зависимость, у которой версия Azure Spring Cloud совпадает с используемой в вашей среде.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Зависимость для Azure Spring Cloud версии 2.1

Для Spring Boot версии 2.1 добавьте следующую зависимость в файл POM приложения.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.2</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Зависимость для Azure Spring Cloud версии 2.2

Для Spring Boot версии 2.2 добавьте следующую зависимость в файл POM приложения.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.1</version>
</dependency>
```

Для пружинной загрузки версии 2,3 Добавьте следующую зависимость в файл POM приложения.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.3.0</version>
</dependency>
```

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Другие рекомендуемые зависимости для включения функций Azure Spring Cloud

Чтобы включить встроенные функции Azure Spring Cloud в реестре служб для распределенной трассировки, вам также понадобится добавить в приложение следующие зависимости. Некоторые из них можно не добавлять, если для отдельных приложений не требуются соответствующие функции.

### <a name="service-registry"></a>Реестр служб

Чтобы использовать управляемую службу реестра служб Azure, добавьте в файл pom.xml зависимость `spring-cloud-starter-netflix-eureka-client`, как показано ниже.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Конечная точка сервера реестра служб будет автоматически внедрена в приложение в виде переменных среды. Приложения смогут зарегистрироваться на сервере реестра служб и обнаруживать другие зависимые микрослужбы.

#### <a name="enablediscoveryclient-annotation"></a>Заметка EnableDiscoveryClient

Добавьте следующую заметку в исходный код приложения.
```java
@EnableDiscoveryClient
```
См. более ранние примеры для приложения PiggyMetrics:
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>Распределенная конфигурация

Чтобы включить распределенную конфигурацию, добавьте зависимость `spring-cloud-config-client` в раздел зависимостей файла pom.xml.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Не указывайте `spring.cloud.config.enabled=false` в конфигурации начальной загрузки. В противном случае приложение не сможет взаимодействовать с сервером конфигурации.

### <a name="metrics"></a>Метрики

Добавьте зависимость `spring-boot-starter-actuator` в раздел зависимостей файла pom.xml, как показано ниже.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Метрики периодически извлекаются из конечных точек JMX. Эти метрики можно визуализировать с помощью портала Azure.

 > [!WARNING]
 > Укажите `spring.jmx.enabled=true` в свойстве конфигурации. В противном случае не удается выполнить визуализацию метрик в портал Azure.

### <a name="distributed-tracing"></a>Распределенная трассировка

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

 Также вам нужно включить экземпляр Azure Application Insights для работы с экземпляром службы Azure Spring Cloud. Сведения об использовании Application Insights с Azure Веснным облаком см. в [документации по распределенной трассировке](spring-cloud-tutorial-distributed-tracing.md).

## <a name="see-also"></a>См. также раздел
* [Анализ журналов и метрик приложений](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [Настройка сервера конфигурации](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Использование распределенной трассировки в Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Краткое руководство по Spring](https://spring.io/quickstart)
* [Документация по Spring Boot](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Дальнейшие действия

Из этой статьи вы узнали, как настроить приложение Java Spring для развертывания в Azure Spring Cloud. Сведения о настройке экземпляра сервера конфигурации см. в разделе [Настройка экземпляра сервера конфигурации](spring-cloud-tutorial-config-server.md).

Дополнительные примеры доступны на GitHub: [Примеры для Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
