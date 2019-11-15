---
title: Краткое руководство. Создание экземпляра Azure Database Migration Service с помощью портала Azure | Документация Майкрософт
description: Использование портала Azure для создания экземпляра Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/05/2019
ms.openlocfilehash: 6427de61ebf30a5e4a62070becd1cc1f750f6c1b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571601"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Краткое руководство. Создание экземпляра службы Azure Database Migration Service с помощью портала Azure

В этом кратком руководстве вы создадите экземпляр Azure Database Migration Service с помощью портала Azure.  После создания службы вы сможете использовать ее для переноса данных с локального сервера SQL Server в Базу данных SQL Azure.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Откройте веб-браузер, перейдите к [порталу Microsoft Azure](https://portal.azure.com/) и введите учетные данные для входа на портал.

Панель мониторинга службы является представлением по умолчанию.

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

        Изучите дополнительные сведения о [создании виртуальной сети с помощью портала Azure](https://aka.ms/vnet).

    - Выберите "Basic: 1 vCore" (Базовый: 1 виртуальное ядро) для параметра **Ценовая категория**.

        ![Создание службы миграции](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. Нажмите кнопку **Создать**.

    Через несколько секунд экземпляр Azure Database Migration Service будет создан и готов к использованию. Экземпляр Azure Database Migration Service отобразится, как показано на рисунке ниже.

    ![Созданная служба Migration Service](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы очистить ресурсы, созданные при работе с этим кратким руководством, удалите [группу ресурсов Azure](../azure-resource-manager/resource-group-overview.md). Чтобы удалить группу ресурсов, перейдите к созданному экземпляру Azure Database Migration Service. Выберите имя **группы ресурсов** и щелкните **Удалить группу ресурсов**. Это действие удаляет все ресурсы в группе ресурсов, а также саму группу.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Миграция с SQL Server в Базу данных SQL Azure](tutorial-sql-server-to-azure-sql.md)