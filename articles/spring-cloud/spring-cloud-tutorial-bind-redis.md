---
title: Привязка кэша Azure для Redis к приложению Azure Spring Cloud | Документация Майкрософт
description: Узнайте, как привязать кэш Azure для Redis к приложению Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d8fa31207baecc80674fb11b492927800676b8a2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038243"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Руководство по Привяжите службы Azure к приложению Azure Spring Cloud: Кэш Redis для Azure

С помощью Azure Spring Cloud можно автоматически привязать выбранные службы Azure к приложениям, а не вручную настраивать приложение Spring Boot. В этой статье показано, как привязать приложение к кэшу Azure для Redis.

## <a name="prerequisites"></a>Предварительные требования

* Развернутый облачный экземпляр Azure Spring Cloud
* Экземпляр службы кэша Azure для Redis
* Расширение Azure Spring Cloud для Azure CLI

При необходимости установите расширение Azure Spring Cloud для Azure CLI с помощью следующей команды:

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье.  Она содержит предварительно установленные общие инструменты Azure вместе с новейшими версиями Git, JDK, Maven и Azure CLI. Если вы вошли в подписку Azure, запустите [Azure Cloud Shell](https://shell.azure.com) на сайте shell.azure.com.  Дополнительные сведения об Azure Cloud Shell см. в [нашей документации](../cloud-shell/overview.md)

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