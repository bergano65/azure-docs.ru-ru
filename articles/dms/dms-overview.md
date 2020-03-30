---
title: Что такое служба Azure Database Migration Service?
description: Обзор миграционной службы базы данных Azure, которая обеспечивает бесшовные миграции из многих источников баз данных в платформы Данных Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 8607f1a687f2979c37c67099874288c6c5ead164
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647498"
---
# <a name="what-is-azure-database-migration-service"></a>Что такое служба Azure Database Migration Service?

Служба миграции баз данных Azure — это полностью управляемая служба, предназначенная для беспрепятственного миграции из нескольких источников баз данных в платформы данных Azure с минимальным временем простоя (онлайн-миграции).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Миграция баз данных в Azure с помощью привычных средств

Миграционная служба Лазурных баз данных интегрирует некоторые функциональные возможности наших существующих инструментов и служб. Она предоставляет пользователям комплексное решение с высокой доступностью. Служба использует [Помощник по миграции данных](https://aka.ms/dma) для создания экспертных отчетов с рекомендациями по изменениям, которые необходимы для переноса данных. Все необходимые исправления выполняются пользователями. Когда вы готовы начать процесс миграции, служба миграции базы данных Azure выполняет все необходимые шаги. Запустив перенос, вы можете больше не думать об этом процессе. Все будет сделано автоматически в соответствии с рекомендациями корпорации Майкрософт.

> [!NOTE]
> Чтобы выполнить сетевую миграцию с помощью Azure Database Migration Service, требуется создать экземпляр ценовой категории "Премиум".

## <a name="regional-availability"></a>Доступность по регионам

Для получения актуальной информации о региональной доступности службы миграции баз данных Azure [см.](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)

## <a name="pricing"></a>Цены

Актуальные сведения о ценах на Azure Database Migration Service см. [на этой странице с ценами](https://azure.microsoft.com/pricing/details/database-migration/).

## <a name="next-steps"></a>Дальнейшие действия

* [Статус сценариев миграции, поддерживаемых миграционной службой базы данных Azure.](resource-scenario-status.md)
* [Создайте экземпляр миграционной службы базы данных Azure с помощью портала Azure.](quickstart-create-data-migration-service-portal.md)
* [Перемотать сервер S'L в базу данных Azure S'L.](tutorial-sql-server-to-azure-sql.md)
* [Обзор предпосылок для использования миграционной службы базы данных Azure.](pre-reqs.md)
* [Часто задаваемые вопросы об использовании миграционной службы баз данных Azure](faq.md).
* [Службы и инструменты, доступные для сценариев миграции данных.](dms-tools-matrix.md)
