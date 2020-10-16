---
title: Параметризация связанных служб в Фабрике данных Azure
description: Узнайте, как параметризовать связанные службы в Фабрике данных Azure и как во время выполнения передавать динамические значения.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/21/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 081d19cc845750f1392e2c1a14229a51d0df4cbc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91276473"
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

Можно параметризовать любой тип связанной службы.
При создании связанной службы в пользовательском интерфейсе фабрика данных предоставляет встроенные возможности параметризации для соединителей следующих типов. В колонке создание и изменение связанной службы можно найти параметры для новых параметров и добавить динамическое содержимое.

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

Для других типов можно параметризовать связанную службу, отредактировав JSON в пользовательском интерфейсе:

- В колонке создание и изменение связанной службы > разверните узел "Дополнительно" в нижней > установите флажок "указать динамическое содержимое в формате JSON"-> укажите полезные данные JSON связанной службы. 
- Кроме того, после создания связанной службы без параметризации в [концентраторе управления](author-visually.md#management-hub) — > связанные службы — > найти конкретную связанную службу > нажмите кнопку "код" (кнопка " {} "), чтобы изменить JSON. 

См. [пример JSON](#json) , чтобы добавить ` parameters` раздел для определения параметров и ссылки на параметр с помощью ` @{linkedService().paraName} ` .

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
