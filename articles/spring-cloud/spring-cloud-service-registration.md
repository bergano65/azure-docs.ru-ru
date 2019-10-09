---
title: Автоматизация реестра и обнаружения служб
description: Узнайте, как автоматизировать обнаружение и регистрацию служб с помощью реестра пружины облачной службы
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: jeconnoc
ms.openlocfilehash: 72327e116e498ce0f6881a5c585a08e56c8bf8c2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038747"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Обнаружение и регистрация облачных служб весны

Обнаружение службы является ключевым требованием для архитектуры на основе микрослужб.  Настройка каждого клиента вручную занимает некоторое время и дает возможность человеческим ошибкам.  Эта проблема решается в реестре облачной службы Azure весны.  После настройки сервер реестра службы будет управлять регистрацией и обнаружением служб для микрослужб вашего приложения. Сервер реестра служб поддерживает реестр развернутых микрослужб, обеспечивает балансировку нагрузки на стороне клиента и отделяет поставщиков услуг от клиентов, не полагаясь на DNS.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Регистрация приложения с помощью реестра пружины облачной службы

Прежде чем приложение сможет управлять регистрацией и обнаружением служб с помощью реестра пружины облачной службы, в файл *POM. XML* приложения должны быть добавлены несколько зависимостей.

Чтобы начать, добавьте репозиторий моментальных снимков в раздел *репозитория* файла *POM. XML.*

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
```

## <a name="include-dependencies"></a>Включить зависимости

Далее мы включаем зависимости для *пружины-Cloud-Starter-Netflix-Еурека-Client* и *пружины-Cloud-Starter-Azure-пружины-Cloud-Client* в *POM. XML.*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0-SNAPSHOT</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>Обновление класса верхнего уровня

Наконец, мы добавим аннотацию к классу верхнего уровня приложения.

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

Конечная точка сервера реестра пружины облачной службы будет внедрена в приложение в качестве переменной среды.  Микрослужбы теперь смогут регистрироваться на сервере реестра службы и обнаруживать другие зависимые микрослужбы.

Обратите внимание, что для распространения изменений с сервера на все микрослужбы может потребоваться несколько минут.
