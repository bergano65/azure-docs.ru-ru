---
title: Автоматизация реестра услуг и обнаружения
description: Узнайте, как автоматизировать обнаружение и регистрацию службы с помощью весеннего облачного реестра служб
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.openlocfilehash: 6c217096f0ba4200f49bd1fd8056768a6f6f6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278854"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Откройте для себя и зарегистрируйте службы Spring Cloud

Service Discovery является ключевым требованием для архитектуры, основанной на микрослужбах.  Настройка каждого клиента вручную требует времени и вводит возможность человеческой ошибки.  Реестр облачных служб Azure Spring решает эту проблему.  После настройки сервер реестра услуг будет контролировать регистрацию и обнаружение услуг для микрослужб вашего приложения. Сервер service Registry ведет реестр развернутых микрослужб, обеспечивает балансировку нагрузки на клиентскую сторону и отнимает поставщиков услуг с клиентами, не полагаясь на DNS.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Зарегистрируйте свое приложение с помощью весеннего реестра облачных служб

Прежде чем приложение сможет управлять регистрацией и открытием служб с помощью Spring Cloud Service Registry, несколько зависимостей должны быть включены в файл *приложения pom.xml.*
Включите в ваш *pom.xml* зависимости для *spring-cloud-starter-netflix-eureka-client* и *spring-cloud-starter-azure-spring-cloud-client*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>Обновление класса высшего уровня

Наконец, мы добавляем аннотацию к высшему классу вашего приложения

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

Конечная точка сервера Spring Cloud Service Registry будет введена в качестве переменной среды в приложении.  Теперь микрослужбы смогут регистрироваться на сервере реестра услуг и открывать другие зависимые микрослужбы.

Обратите внимание, что распространение изменений с сервера на все микрослужбы может занять несколько минут.
