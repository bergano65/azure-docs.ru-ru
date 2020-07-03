---
title: Учебник по привязке Кэша Azure для Redis к приложению Azure Spring Cloud
description: В этом учебнике описано, как привязать Кэш Azure для Redis к приложению Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: 94f7b5a2363b7c53e0f70500e5a0a8cb6f64e611
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277507"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Привязка Кэша Azure для Redis к приложению Azure Spring Cloud 

Чтобы не настраивать приложения Spring Boot вручную, вы можете автоматически привязать выбранные службы Azure к приложениям с помощью Azure Spring Cloud. В этой статье показано, как привязать приложение к Кэшу Azure для Redis.

## <a name="prerequisites"></a>предварительные требования

* Развернутый облачный экземпляр Azure Spring Cloud
* Экземпляр службы кэша Azure для Redis
* Расширение Azure Spring Cloud для Azure CLI.

Если у вас еще не развернут экземпляр Azure Spring Cloud, выполните процедуру в [этом кратком руководстве](spring-cloud-quickstart-launch-app-portal.md), чтобы развернуть приложение Azure Spring Cloud.

## <a name="bind-azure-cache-for-redis"></a>Привязка кэша Azure для Redis

1. Добавьте следующую зависимость в файл pom.xml. проекта:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Удалите все свойства `spring.redis.*` из файла `application.properties`.

1. Обновите текущую развернутую службу с помощью `az spring-cloud app update` или создайте новое развертывание с помощью `az spring-cloud app deployment create`.

1. Перейдите к странице своей службы Azure Spring Cloud на портале Azure. Перейдите на **панель мониторинга приложений** и выберите приложение для привязки к Кэшу Azure для Redis. Это то же приложение, которое вы обновили или развернули на предыдущем шаге.

1. Выберите **Service binding** (Привязка службы), а затем — **Создание привязки службы**. Заполняя форму, не забудьте выбрать в качестве **типа привязки** значение **Кэш Azure для Redis**, а также сервер Кэша Azure для Redis и **первичный** ключ.

1. Перезапустите приложение. Теперь привязка должна работать.

1. Чтобы обеспечить правильную привязку службы, выберите имя привязки и проверьте сведения о ней. Поле `property` должно выглядеть следующим образом:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Дальнейшие действия

Работая с этим учебником, вы узнали, как привязать приложение Azure Spring Cloud к Кэшу Azure для Redis. Дополнительные сведения о привязке служб к приложению см. в учебнике по привязке приложения к экземпляру Базы данных Azure для MySQL.

> [!div class="nextstepaction"]
> [Учебник по привязке к экземпляру Базы данных Azure для MySQL.](spring-cloud-tutorial-bind-mysql.md)
