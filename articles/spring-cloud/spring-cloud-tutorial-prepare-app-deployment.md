---
title: Как подготовить приложение к развертыванию в Azure Веснного облака
description: Узнайте, как подготовить приложение к развертыванию в Azure Веснного облака.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: ff0582e3c4f654ed2a7f5efdc9ce8fd7a226595a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906841"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Подготовка приложения к развертыванию в Azure Веснного облака

::: zone pivot="programming-language-csharp"
Azure Веснное облако предоставляет надежные службы для размещения, мониторинга, масштабирования и обновления приложения Стилтое. В этой статье показано, как подготовить существующее приложение Стилтое для развертывания в Azure Веснного облака. 

В этой статье объясняются зависимости, конфигурация и код, необходимые для запуска приложения .NET Core Стилтое в Azure Веснного облака. Сведения о том, как развернуть приложение в Azure Веснного облака, см. в статье [развертывание первого облачного приложения Azure весны](spring-cloud-quickstart.md).

>[!Note]
> В настоящее время поддержка Steeltoe для Azure Spring Cloud предлагается в качестве общедоступной предварительной версии. Предложения общедоступной предварительной версии позволяют клиентам экспериментировать с новыми функциями до официального выпуска.  Общедоступные предварительные версии функций и служб не предназначены для использования в рабочей среде.  Чтобы получить дополнительные сведения о поддержке на этапе использования предварительных версий, ознакомьтесь с разделом [Вопросы и ответы](https://azure.microsoft.com/support/faq/) или оформите [запрос на поддержку](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

##  <a name="supported-versions"></a>Поддерживаемые версии

Azure Веснного облака поддерживает:

* .NET Core 3.1
* Стилтое 2,4

## <a name="dependencies"></a>Зависимости

Установите пакет [Microsoft. Azure. спрингклауд. Client](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) .

## <a name="update-programcs"></a>Обновление Program.cs

В `Program.Main` методе вызовите `UseAzureSpringCloudService` метод:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .UseAzureSpringCloudService();
```

## <a name="enable-eureka-server-service-discovery"></a>Включить обнаружение службы Еурека Server

В источнике конфигурации, который будет использоваться при запуске приложения в Azure Веснного облака, укажите `spring.application.name` то же имя, что и облачное приложение Azure весны, в котором будет развернут проект.

Например, при развертывании проекта .NET с именем `EurekaDataProvider` в облачном приложении Azure весны с `planet-weather-provider` именем *appSettings.jsдля* файла должен быть указан следующий код JSON:

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>Использовать обнаружение служб

Чтобы вызвать службу с помощью обнаружения службы Еурека Server, выполните HTTP-запросы к, `http://<app_name>` где `app_name` — это значение `spring.application.name` целевого приложения. Например, следующий код вызывает `planet-weather-provider` службу:

```csharp
using (var client = new HttpClient(discoveryHandler, false))
{
    var responses = await Task.WhenAll(
        client.GetAsync("http://planet-weather-provider/weatherforecast/mercury"),
        client.GetAsync("http://planet-weather-provider/weatherforecast/saturn"));
    var weathers = await Task.WhenAll(from res in responses select res.Content.ReadAsStringAsync());
    return new[]
    {
        new KeyValuePair<string, string>("Mercury", weathers[0]),
        new KeyValuePair<string, string>("Saturn", weathers[1]),
    };
}
```
::: zone-end

::: zone pivot="programming-language-java"
В этой статье рассказывается, как подготовить имеющееся приложение Java Spring к развертыванию в Azure Spring Cloud. При правильной настройке Azure Spring Cloud предоставляет надежные службы для мониторинга, масштабирования и обновления приложения Java Spring Cloud.

Перед выполнением этого примера вы можете ознакомиться с [базовым кратким руководством](spring-cloud-quickstart.md).

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
2.2 | Хокстон. SR8
2.3 | Хокстон. SR8

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
                <version>Greenwich.RELEASE</version>
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
                <version>Hoxton.SR8</version>
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
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
## <a name="azure-spring-cloud-client-dependency"></a>Зависимость клиента Azure Spring Cloud

Azure Spring Cloud размещает компоненты Spring Cloud и управляет ими от вашего имени. К ним относятся реестр облачной службы Spring Cloud и сервер конфигурации Spring Cloud. Рекомендуется использовать пружинную загрузку 2,2 или 2,3. Для пружинной загрузки 2,1 необходимо включить клиентскую библиотеку Azure "Весна Cloud Client" в зависимости, чтобы обеспечить взаимодействие с экземпляром облачной службы Azure весны.

В следующей таблице перечислены нужные версии Azure Spring Cloud для приложения, в котором используются Spring Boot и Spring Cloud.

Версия Spring Boot | Версия Spring Cloud | Начальная версия облачного клиента Azure весны
---|---|---
2.1.x | Greenwich.RELEASE | 2.1.2
2.2. x | Хокстон. SR8 | Не требуется.
2.3. x | Хокстон. SR8 | Не требуется.

Если вы используете пружинную загрузку 2,1, включите следующие депенденЦии в файл pom.xml.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.2</version>
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
::: zone-end
