---
title: Обзор Azure Database Migration Service | Документация Майкрософт
description: Обзор службы Azure Database Migration Service, которая обеспечивает непрерывную миграцию из множества источников баз данных на платформы данных Azure.
services: database-migration
author: pochiraju
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 0b1c45fcb030ac31934a5fb7aad478ef08d66129
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478191"
---
# <a name="what-is-azure-database-migration-service"></a>Что такое служба Azure Database Migration Service?

Azure Database Migration Service — это полностью управляемая служба, которая выполняет непрерывную миграцию из множества источников баз данных на платформы данных Azure с минимальным временем простоя (online миграции).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Миграция баз данных в Azure с помощью привычных средств

Azure Database Migration Service объединяет некоторые функциональные возможности существующих средств и служб. Она предоставляет пользователям комплексное решение с высокой доступностью. Служба использует [Помощник по миграции данных](https://aka.ms/dma) для создания экспертных отчетов с рекомендациями по изменениям, которые необходимы для переноса данных. Все необходимые исправления выполняются пользователями. Когда вы будете готовы приступить к переносу, Azure Database Migration Service выполнит все необходимые действия. Запустив перенос, вы можете больше не думать об этом процессе. Все будет сделано автоматически в соответствии с рекомендациями корпорации Майкрософт.

> [!NOTE]
> Чтобы выполнить сетевую миграцию с помощью Azure Database Migration Service, требуется создать экземпляр ценовой категории "Премиум".

## <a name="regional-availability"></a>Доступность по регионам

Актуальные сведения о региональной доступности службы Azure Database Migration Service, см. в разделе [доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration).

## <a name="pricing"></a>Цены

Актуальные сведения о ценах на Azure Database Migration Service см. [на этой странице с ценами](https://azure.microsoft.com/pricing/details/database-migration/).

## <a name="next-steps"></a>Дальнейшие действия

* [Состояние миграции, поддерживаемые службой Azure Database Migration Service](resource-scenario-status.md).
* [Создайте экземпляр Azure Database Migration Service с помощью портала Azure](quickstart-create-data-migration-service-portal.md).
* [Миграция с SQL Server в базу данных SQL Azure](tutorial-sql-server-to-azure-sql.md).
* [Предварительные требования для с помощью Azure Database Migration Service](pre-reqs.md).
* [Часто задаваемые вопросы о службе Azure Database Migration Service](faq.md).
* [Доступные службы и средства для сценариев переноса данных](dms-tools-matrix.md)
