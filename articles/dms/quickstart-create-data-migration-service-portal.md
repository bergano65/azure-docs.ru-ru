---
title: Краткое руководство. Создание экземпляра с помощью портала Azure
titleSuffix: Azure Database Migration Service
description: Использование портала Azure для создания экземпляра Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 07/21/2020
ms.openlocfilehash: ff9fc2baaf1563d4a02364db00344ffc0bc46a6a
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060271"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Краткое руководство. Создание экземпляра службы Azure Database Migration Service с помощью портала Azure

В этом кратком руководстве вы создадите экземпляр Azure Database Migration Service с помощью портала Azure.  Созданный экземпляр можно будет использовать для переноса данных с платформы SQL Server в Базу данных SQL Azure.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Откройте веб-браузер, перейдите к [порталу Microsoft Azure](https://portal.azure.com/) и введите учетные данные для входа на портал.

Панель мониторинга службы является представлением по умолчанию.

> [!NOTE]
> В одной подписке можно создать до 10 экземпляров DMS на регион. Если требуется больше экземпляров, создайте запрос в службу поддержки.

## <a name="register-the-resource-provider"></a>Регистрация поставщика ресурсов

Прежде чем создать свой первый экземпляр Database Migration Service, зарегистрируйте поставщик ресурсов Microsoft.DataMigration.

1. На портале Azure щелкните **Все службы** и выберите **Подписки**.

2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.

3. В поле поиска введите migration, а затем справа от **Microsoft.DataMigration** щелкните **Зарегистрировать**.

    ![Регистрация поставщика ресурсов](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Создание экземпляра службы

1. Щелкните **+Создать ресурс**, чтобы создать экземпляр Azure Database Migration Service.

2. Выполните в Marketplace поиск по слову migration, выберите службу **Azure Database Migration Service**, а затем на экране **Azure Database Migration Service** нажмите кнопку **Create**.

3. На экране **Создать Migration Service** сделайте следующее:

    - Выберите **имя службы**, которое хорошо запоминается и будет уникальным для идентификации экземпляра Azure Database Migration Service.
    - Выберите **подписку** Azure, в которой нужно создать экземпляр.
    - Выберите существующую **группу ресурсов** или создайте новую.
    - Выберите **расположение**, наиболее близкое к исходному или целевому серверу.
    - Выберите существующую **виртуальную сеть** или создайте новую.

        Виртуальная сеть предоставляет Azure Database Migration Service доступ к базе данных-источнику и целевой среде.

        См. дополнительные сведения о [создании виртуальной сети с помощью портала Azure](../virtual-network/quick-create-portal.md).

    - Выберите "Basic: 1 vCore" (Базовый: 1 виртуальное ядро) для параметра **Ценовая категория**.

        ![Создание службы миграции](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. Нажмите кнопку **создания**.

    Через несколько секунд экземпляр Azure Database Migration Service будет создан и готов к использованию. Экземпляр Azure Database Migration Service отобразится, как показано на рисунке ниже.

    ![Созданная служба Migration Service](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы очистить ресурсы, созданные при работе с этим кратким руководством, удалите [группу ресурсов Azure](../azure-resource-manager/management/overview.md). Чтобы удалить группу ресурсов, перейдите к созданному экземпляру Azure Database Migration Service. Выберите имя **группы ресурсов** и щелкните **Удалить группу ресурсов**. Это действие удаляет все ресурсы в группе ресурсов, а также саму группу.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Перенос SQL Server в Базу данных Azure SQL](tutorial-sql-server-to-azure-sql.md)