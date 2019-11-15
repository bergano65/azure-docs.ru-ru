---
title: Привязка кэша Azure для Redis к приложению Azure Spring Cloud | Документация Майкрософт
description: Узнайте, как привязать кэш Azure для Redis к приложению Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: a901e4194909df85f53799d5937515e42ea87a69
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607571"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Руководство по Привяжите службы Azure к приложению Azure Spring Cloud: Кэш Redis для Azure

С помощью Azure Spring Cloud можно автоматически привязать выбранные службы Azure к приложениям, а не вручную настраивать приложение Spring Boot. В этой статье показано, как привязать приложение к кэшу Azure для Redis.

## <a name="prerequisites"></a>Предварительные требования

* Развернутый облачный экземпляр Azure Spring Cloud
* Экземпляр службы кэша Azure для Redis
* Расширение Azure Spring Cloud для Azure CLI

Если у вас еще не развернут экземпляр Azure Spring Cloud, выполните процедуру в [этом кратком руководстве](spring-cloud-quickstart-launch-app-portal.md), чтобы развернуть первое приложение Spring Cloud.

## <a name="bind-azure-cache-for-redis"></a>Привязка кэша Azure для Redis

1. Добавьте в `pom.xml` проекта следующую зависимость

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Удалите свойства `spring.redis.*` (если они есть) в файле `application.properties`

1. Обновите текущую развернутую службу с помощью `az spring-cloud app update` или создайте новое развертывание с помощью `az spring-cloud app deployment create`.

1. Перейдите к странице своей службы Azure Spring Cloud на портале Azure. Найдите **панель мониторинга приложения** и выберите приложение для привязки к кэшу Azure для Redis.  Это то же приложение, которое вы обновили или развернули на предыдущем шаге. Затем выберите `Service binding` и нажмите кнопку `Create service binding`. Заполните форму, убедившись, что выбран параметр **Binding Type** `Azure Cache for Redis`, сервер Redis и первичный ключ. 

1. Перезапустите приложение, и эта привязка должна начать работать прямо сейчас.

1. Чтобы обеспечить правильную привязку службы, выберите имя привязки и проверьте сведения о ней. Поле `property` должно выглядеть следующим образом:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве описывается, как привязать приложение Azure Spring Cloud к кэшу Redis для Azure.  Дополнительные сведения о привязке служб к приложению см. в руководстве по привязке приложения к базе данных MySQL.

> [!div class="nextstepaction"]
> [Как привязать службу Azure MySQL к службе Azure Spring Cloud](spring-cloud-tutorial-bind-mysql.md).