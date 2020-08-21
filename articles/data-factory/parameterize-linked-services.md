---
title: Параметризация связанных служб в Фабрике данных Azure
description: Узнайте, как параметризовать связанные службы в Фабрике данных Azure и как во время выполнения передавать динамические значения.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/21/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: b3aadab1b4af80f98c57a279b69606a02846e996
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716849"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Параметризация связанных служб в Фабрике данных Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Теперь можно параметризовать связанную службу и передавать динамические значения во время выполнения. Например, если вы хотите подключиться к разным базам данных на одном логическом сервере SQL Server, теперь можно параметризовать имя базы данных в определении связанной службы. Это не позволит вам создавать связанную службу для каждой базы данных на логическом сервере SQL Server. Например, можно параметризовать другие свойства в определении связанной службы, например *Имя пользователя*.

Пользовательский интерфейс фабрики данных можно использовать в портал Azure или в программном интерфейсе для параметризации связанных служб.

> [!TIP]
> Мы рекомендуем не параметризировать пароли или секреты. Вместо этого храните все строки подключения в Azure Key Vault и параметризируйте *Имя секрета*.

Уделите 7 минут своего времени, чтобы просмотреть следующее видео с кратким обзором и демонстрацией этой функции.

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Поддерживаемые хранилища данных

В настоящее время параметризация связанной службы поддерживается в пользовательском интерфейсе фабрики данных для следующих хранилищ данных. Для всех других хранилищ данных можно параметризовать связанную службу, выбрав значок **Код** на вкладке **Подключения**, а также использовать редактор JSON.

- Amazon Redshift
- Azure Cosmos DB (API SQL)
- База данных Azure для MySQL
- База данных SQL Azure
- Azure Synapse Analytics (ранее — Хранилище данных SQL)
- MySQL
- Oracle;
- SQL Server
- Базовый протокол HTTP
- Базовый протокол REST

## <a name="data-factory-ui"></a>Пользовательский интерфейс Фабрики данных

![Добавление динамического содержимого к определению связанной службы](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Создание параметра](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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
