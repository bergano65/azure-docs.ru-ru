---
title: Сведения о перемещении учетной записи Azure Cosmos DB в другой регион
description: Сведения о перемещении учетной записи Azure Cosmos DB в другой регион
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 60c28a96008355491c058cd08dbbb3a1cbffad98
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059455"
---
# <a name="how-to-move-an-azure-cosmos-db-account-to-another-region"></a>Перемещение учетной записи Azure Cosmos DB в другой регион

В этой статье описывается, как либо переместить регион, где данные реплицируются в Azure Cosmos DB, либо как перенести метаданные учетной записи (Azure Resource Manager), а также данные из одного региона в другой.

## <a name="move-data-from-one-region-to-another"></a>Перемещение данных из одного региона в другой

Azure Cosmos DB поддерживает встроенную репликацию данных, поэтому перемещение данных из одного региона в другой является простым и может быть выполнено с помощью портал Azure, Azure PowerShell или Azure CLI и состоит из следующих шагов:

1. Добавление нового региона в учетную запись

    Добавление нового региона в учетную запись Azure Cosmos DB см. в статье [Добавление и удаление регионов в учетной записи Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

1. Выполнение перехода на другой ресурс вручную в новый регион

    В ситуациях, когда удаляемый регион в настоящее время является регионом записи для учетной записи, необходимо инициировать отработку отказа в новый добавленный регион. Это операция с нулевым временем простоя. Если вы перемещаете область чтения в учетной записи с несколькими регионами, этот шаг можно пропустить. Чтобы начать отработку отказа, см. [Выполнение перехода на другой ресурс вручную в учетной записи Azure Cosmos](how-to-manage-database-account.md#manual-failover)

1. Удалить исходный регион

    Сведения об удалении региона из учетной записи Azure Cosmos DB см. в статье [Добавление и удаление регионов в учетной записи Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

## <a name="migrate-azure-cosmos-db-account-meta-data"></a>Миграция метаданных учетной записи Azure Cosmos DB

Azure Cosmos DB изначально не поддерживает миграцию метаданных учетной записи из одного региона в другой. Чтобы перенести метаданные и данные клиента из одного региона в другой, в нужном регионе должна быть создана новая учетная запись, а затем данные должны быть скопированы вручную. Миграция почти нулевого времени простоя для API SQL требует использования [пр](change-feed.md) или средства, использующего его. Если вы выполняете миграцию MongoDB API, Cassandra или другого API или дополнительные сведения о параметрах при переносе данных между учетными записями, см. раздел [Параметры переноса локальных или облачных данных в Azure Cosmos DB](cosmosdb-migrationchoices.md). Ниже показано, как перенести учетную запись Azure Cosmos DB для API SQL и его данных из одного региона в другой:

1. Создание новой учетной записи Azure Cosmos DB в нужном регионе

    Чтобы создать новую учетную запись с помощью портал Azure, PowerShell или CLI, см. раздел [Создание учетной записи Azure Cosmos DB](how-to-manage-database-account.md#create-an-account).

1. Создание новой базы данных и контейнера

    Чтобы создать новую базу данных и контейнер, см. раздел [Создание контейнера Azure Cosmos](how-to-create-container.md) .

1. Перенос данных с помощью средства Azure Cosmos DB Live Data Migrator

    Чтобы перенести данные с почти нулевым временем простоя, см. [Azure Cosmos DB Live Data Migrator Tool](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)

1. Обновить строку подключения приложения

    Когда средство Live Migrator продолжает работать, обновите сведения о подключении в новом развертывании приложений. Конечные точки и ключи для приложения можно получить из портал Azure.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Управление доступом (IAM) на портале Azure: демонстрация безопасности базы данных NoSQL":::

1. Перенаправлять запросы в новое приложение

    После подключения нового приложения к Azure Cosmos DB можно перенаправить клиентские запросы в новое развертывание.

1. Удалить все ресурсы, которые больше не нужны

    Теперь, когда запросы полностью перенаправляются на новый экземпляр, можно удалить старую учетную запись Azure Cosmos DB и средство Live Data Migrator.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения и примеры по управлению учетной записью Azure Cosmos, а также базами данных и контейнерами см. в следующих статьях:

* [Управление учетной записью Azure Cosmos](how-to-manage-database-account.md)
* [Веб-канал изменений в Azure Cosmos DB](change-feed.md)
