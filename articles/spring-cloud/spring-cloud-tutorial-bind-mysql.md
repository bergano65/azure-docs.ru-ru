---
title: Как привязать базу данных Azure для MySQL к приложению Azure Spring Cloud | Документация Майкрософт
description: В этой статье показано, как привязать Azure MySQL к приложению Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: e2add139f5cfd8299ec809793dd822b051d0f542
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038353"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>Руководство по Привяжите службы Azure к приложению Azure Spring Cloud: База данных Azure для MySQL

С помощью Azure Spring Cloud можно автоматически привязать выбранные службы Azure к приложениям, а не вручную настраивать приложение Spring Boot. В этом учебнике показано, как привязать приложение к Azure MySQL.

## <a name="prerequisites"></a>Предварительные требования

* Развернутый облачный экземпляр Azure Spring Cloud
* Учетная запись базы данных Azure для MySQL
* Инфраструктура CLI Azure

При необходимости установите расширение Azure Spring Cloud для Azure CLI с помощью следующей команды:

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье.  Она содержит предварительно установленные общие инструменты Azure вместе с новейшими версиями Git, JDK, Maven и Azure CLI. Если вы вошли в подписку Azure, запустите [Azure Cloud Shell](https://shell.azure.com) на сайте shell.azure.com.  Дополнительные сведения об Azure Cloud Shell см. в [нашей документации](../cloud-shell/overview.md)

## <a name="bind-azure-database-for-mysql"></a>Привязка базы данных Azure для MySQL

1. Обратите внимание на имя и пароль администратора учетной записи Azure MySQL. Подключитесь к серверу и создайте базу данных с именем `testdb` из клиента MySQL. Создайте новую учетную запись без прав администратора.

1. Добавьте в `pom.xml` проекта следующую зависимость

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Удалите свойства `spring.datasource.*` (если они есть) в файле `application.properties`.

1. Обновите текущую развернутую службу с помощью `az spring-cloud app update` или создайте новое развертывание для этого изменения с помощью `az spring-cloud app deployment create`.  Эти команды либо обновляют, либо создают приложение с новой зависимостью.

1. Перейдите к странице своей службы Azure Spring Cloud на портале Azure. Найдите **панель мониторинга приложения** и выберите приложение для привязки к Azure MySQL.  Это то же приложение, которое вы обновили или развернули на предыдущем шаге. Затем выберите `Service binding` и нажмите кнопку `Create service binding`. Заполните форму, выбрав **Binding Type** `Azure MySQL`, то же самое имя базы данных, которое вы использовали ранее, и те же имя пользователя и пароль, которые были записаны на первом шаге.

1. Перезапустите приложение, и эта привязка должна начать работать прямо сейчас.

1. Чтобы обеспечить правильную привязку службы, выберите имя привязки и проверьте сведения о ней. Поле `property` должно выглядеть следующим образом:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали, как привязать приложение Azure Spring Cloud к базе данных MySQL.  Дополнительные сведения об управлении службой Azure Spring Cloud см. в статье об обнаружении и регистрации служб.

> [!div class="nextstepaction"]
> [Узнайте, как включить обнаружение и регистрацию служб с помощью реестра Azure Spring Cloud](spring-cloud-service-registration.md).

