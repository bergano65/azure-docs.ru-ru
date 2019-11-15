---
title: Как привязать базу данных Azure для MySQL к приложению Azure Spring Cloud | Документация Майкрософт
description: В этой статье показано, как привязать Azure MySQL к приложению Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: b6de5bb3b25c111d1b7775ea9570a4ae2cf45042
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607585"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>Руководство по Привяжите службы Azure к приложению Azure Spring Cloud: База данных Azure для MySQL

С помощью Azure Spring Cloud можно автоматически привязать выбранные службы Azure к приложениям, а не вручную настраивать приложение Spring Boot. В этом учебнике показано, как привязать приложение к Azure MySQL.

## <a name="prerequisites"></a>Предварительные требования

* Развернутый облачный экземпляр Azure Spring Cloud
* Учетная запись базы данных Azure для MySQL
* Инфраструктура CLI Azure

Если у вас еще не развернут экземпляр Azure Spring Cloud, выполните процедуру в [этом кратком руководстве](spring-cloud-quickstart-launch-app-portal.md), чтобы развернуть первое приложение Spring Cloud.

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

