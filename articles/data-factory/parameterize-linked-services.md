---
title: Параметризация связанных служб в Фабрике данных Azure | Документация Майкрософт
description: Узнайте, как параметризовать связанные службы в Фабрике данных Azure и как во время выполнения передавать динамические значения.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: douglasl
ms.openlocfilehash: af06be520b10184c1e96bdb27d0b8b648cde84a2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54018459"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Параметризация связанных служб в Фабрике данных Azure

Теперь можно параметризовать связанную службу и передавать динамические значения во время выполнения. Например, если вы хотите подключиться к разным базам данных на одном сервере Базы данных SQL Azure, можно параметризовать имя базы данных в определении связанной службы. Это позволит вам не создавать связанную службу для каждой базы данных на сервере базы данных Azure SQL. Например, можно параметризовать другие свойства в определении связанной службы, например *Имя пользователя*.

Для параметризации связанных служб можно использовать пользовательский интерфейс Фабрики данных на портале Azure или интерфейс программирования.

> [!TIP]
> Мы рекомендуем не параметризировать пароли или секреты. Вместо этого храните все строки подключения в Azure Key Vault и параметризируйте *Имя секрета*.

Уделите 7 минут своего времени, чтобы просмотреть следующее видео с кратким обзором и демонстрацией этой функции.

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Поддерживаемые хранилища данных

В настоящее время в пользовательском интерфейсе Фабрики данных на портале Azure связанной службы параметризация поддерживается для следующих хранилищ данных. Для всех других хранилищ данных можно параметризовать связанную службу, выбрав значок **Код** на вкладке **Подключения**, а также использовать редактор JSON.
- Базы данных SQL Azure
- Хранилище данных SQL Azure
- SQL Server;
- Oracle
- База данных Cosmos
- Amazon Redshift
- MySQL
- База данных Azure для MySQL

## <a name="data-factory-ui"></a>Пользовательский интерфейс фабрики данных

![Добавление динамического содержимого к определению связанной службы](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Создание параметра](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "value": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
                "type": "SecureString"
            }
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
