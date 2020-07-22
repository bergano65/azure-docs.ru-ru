---
title: Что такое служба Azure Database Migration Service?
description: Обзор Azure Database Migration Service, который обеспечивает плавную миграцию из множества источников базы данных на платформы данных Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 64fb692c7ef896e643db603a6fbc3948cc4b3a11
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84749637"
---
# <a name="what-is-azure-database-migration-service"></a>Что такое служба Azure Database Migration Service?

Azure Database Migration Service — это полностью управляемая служба, предназначенная для обеспечения бесперебойной миграции из нескольких источников базы данных на платформы данных Azure с минимальным временем простоя (оперативная миграция).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Миграция баз данных в Azure с помощью привычных средств

Azure Database Migration Service интегрирует некоторые функции существующих средств и служб. Она предоставляет пользователям комплексное решение с высокой доступностью. Служба использует [Помощник по миграции данных](https://aka.ms/dma) для создания экспертных отчетов с рекомендациями по изменениям, которые необходимы для переноса данных. Все необходимые исправления выполняются пользователями. Когда вы будете готовы начать процесс миграции, Azure Database Migration Service выполняет все необходимые действия. Запустив перенос, вы можете больше не думать об этом процессе. Все будет сделано автоматически в соответствии с рекомендациями корпорации Майкрософт. 

> [!NOTE]
> Чтобы выполнить сетевую миграцию с помощью Azure Database Migration Service, требуется создать экземпляр ценовой категории "Премиум".

## <a name="regional-availability"></a>Доступность по регионам

Актуальные сведения о доступности Azure Database Migration Service см. в разделе [продукты, доступные по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration).

## <a name="pricing"></a>Цены

Актуальные сведения о ценах на Azure Database Migration Service см. [на этой странице с ценами](https://azure.microsoft.com/pricing/details/database-migration/).

## <a name="next-steps"></a>Дальнейшие шаги

* [Состояние сценариев миграции, поддерживаемых Azure Database Migration Service](resource-scenario-status.md).
* [Создайте экземпляр Azure Database Migration Service с помощью портал Azure](quickstart-create-data-migration-service-portal.md).
* [Перенос SQL Server в базу данных SQL Azure](tutorial-sql-server-to-azure-sql.md).
* [Общие сведения о предварительных требованиях для использования Azure Database Migration Service](pre-reqs.md).
* [Часто задаваемые вопросы об использовании Azure Database Migration Service](faq.md).
* [Службы и средства, доступные для сценариев переноса данных](dms-tools-matrix.md).
