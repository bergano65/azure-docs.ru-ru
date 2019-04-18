---
title: Работа с учетными записями Azure Cosmos DB
description: В этой статье описывается, как создать и использовать учетные записи Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: da55807d4ca803adf63a1dd2dfe3ce3794cdd509
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762605"
---
# <a name="work-with-azure-cosmos-account"></a>Работа с учетной записью Azure Cosmos

Azure Cosmos DB — это полностью управляемая платформа как услуга (PaaS). Чтобы начать использовать Azure Cosmos DB, нужно создать учетную запись Azure Cosmos в подписке Azure. Учетная запись Azure Cosmos содержит уникальное DNS-имя. Вы можете управлять ею с помощью портала Azure, Azure CLI или разных языковых пакетов SDK. Дополнительные сведения см. в статье [об управлении учетной записью Azure Cosmos](how-to-manage-database-account.md).

Учетная запись Azure Cosmos — это основная единица глобального распределения и высокого уровня доступности. Для обеспечения глобального распределения данных и высокой пропускной способности в учетной записи Azure Cosmos можно в любое время добавлять и удалять регионы Azure. В учетной записи Azure Cosmos можно настроить один или несколько регионов записи. Дополнительные сведения см. в статье [Управление учетными записями базы данных в Azure Cosmos DB](how-to-manage-database-account.md). В учетной записи Azure Cosmos можно настроить [уровень согласованности](consistency-levels.md) по умолчанию. Azure Cosmos DB предоставляет исчерпывающие Соглашения об уровне обслуживания, включающие пропускную способность, задержки на уровне 99-го процентиля, согласованность и высокую доступность. Дополнительные сведения см. в статье [Соглашение об уровне обслуживания для Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Чтобы безопасно управлять доступом ко всем данным в вашей учетной записи Azure Cosmos, можно использовать [главные ключи](secure-access-to-data.md) связанных с учетной записью. Для обеспечения дополнительной защиты доступа к данным, можно настроить [конечной точки службы виртуальной сети](vnet-service-endpoint.md) и [брандмауэра IP-адресов](firewall-support.md) в вашей учетной записи Azure Cosmos. 

## <a name="elements-in-an-azure-cosmos-account"></a>Элементы в учетной записи Azure Cosmos

В Azure Cosmos DB контейнер — это основная единица масштабируемости. В контейнере у вас может быть практически неограниченная пропускная способность (ЕЗ/с) и ресурсы хранения. Azure Cosmos DB прозрачно разделяет контейнеры с помощью указанного ключа логической секции, чтобы эластично масштабировать подготовленную пропускную способность и ресурсы хранения. Дополнительные сведения см. в статье о работе [с контейнерами и элементами Azure Cosmos](databases-containers-items.md).

В настоящее время в подписке Azure можно создать до 100 учетных записей Azure Cosmos. Одна учетная запись Azure Cosmos может управлять практически неограниченным объемом данных и подготовленной пропускной способностью. Для управления данными и подготовленной пропускной способностью можно создать одну или несколько баз данных Azure Cosmos в учетной записи, а в рамках базы данных создать один или несколько контейнеров. Ниже показана иерархия разных элементов в учетной записи Azure Cosmos:

![Иерархии учетной записи Azure Cosmos](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>Дальнейшие действия

Сведения об управлении вашей учетной записи Azure Cosmos и другие понятия:

* [Управление учетными записями базы данных в Azure Cosmos DB](how-to-manage-database-account.md)
* [Глобальное распределение](distribute-data-globally.md)
* [Уровни согласованности](consistency-levels.md)
* [Работа с базами данных, контейнерами и элементами Azure Cosmos](databases-containers-items.md)
* [Безопасный доступ к учетной записи Azure Cosmos DB с использованием конечной точки службы виртуальной сети Azure](vnet-service-endpoint.md)
* [Поддержка брандмауэра для Azure Cosmos DB](firewall-support.md)
* [Добавление и удаление регионов из учетной записи базы данных](how-to-manage-database-account.md)
* [Соглашение об уровне обслуживания для Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
