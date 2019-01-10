---
title: Обзор Azure Database Migration Service | Документация Майкрософт
description: Обзор службы Azure Database Migration Service, которая обеспечивает непрерывную миграцию из множества источников баз данных на платформы данных Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.topic: article
ms.date: 12/19/2018
ms.openlocfilehash: 68fb6497141d79dc721af7dfa5a8f69102c7e239
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53722206"
---
# <a name="what-is-the-azure-database-migration-service"></a>Что такое Azure Database Migration Service?
Azure Database Migration Service — это полностью управляемая служба, которая выполняет непрерывную миграцию из множества источников баз данных на платформы данных Azure с минимальным временем простоя (миграции в интерактивном режиме).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Миграция баз данных в Azure с помощью привычных средств
Служба Azure Database Migration Service обладает некоторыми функциональными возможностями, которые можно найти в других наших средствах и службах. Она предоставляет пользователям комплексное решение с высокой доступностью. Служба использует [Помощник по миграции данных](https://aka.ms/dma) для создания экспертных отчетов с рекомендациями по изменениям, которые необходимы для переноса данных. Все необходимые исправления выполняются пользователями. Когда вы будете готовы приступить к переносу, Azure Database Migration Service выполнит все необходимые действия. Запустив перенос, вы можете больше не думать об этом процессе. Все будет сделано автоматически в соответствии с рекомендациями корпорации Майкрософт.

> [!NOTE]
> Чтобы выполнить подключенную миграцию с помощью Azure Database Migration Service, требуется создать экземпляр ценовой категории Premium.

## <a name="regional-availability"></a>Доступ по регионам
Актуальные сведения о доступности Azure Database Migration Service в регионах см. на странице [Доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration).

## <a name="pricing"></a>Цены
Актуальные сведения о ценах на Azure Database Migration Service см. [на этой странице с ценами](https://azure.microsoft.com/pricing/details/database-migration/).

## <a name="next-steps"></a>Дополнительная информация
- [Состояние сценариев миграции, поддерживаемых службой Azure Database Migration Service](resource-scenario-status.md)
- [Создание экземпляра службы Database Migration Service с помощью портала Azure](quickstart-create-data-migration-service-portal.md)
- [Миграция с SQL Server в базу данных SQL Azure](tutorial-sql-server-to-azure-sql.md).
- [Overview of prerequisites for using the Azure Database Migration Service](pre-reqs.md) (Предварительные требования для использования службы Azure Database Migration Service).
- [FAQ about using the Azure Database Migration Service](faq.md) (Часто задаваемые вопросы о службе Azure Database Migration Service).
- [Доступные службы и средства для сценариев переноса данных](dms-tools-matrix.md)
