---
title: Обзор Azure Database Migration Service | Документация Майкрософт
description: Обзор службы Azure Database Migration Service, которая обеспечивает непрерывную миграцию из множества источников баз данных на платформы данных Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: douglasl
ms.service: database-migration
ms.workload: data-services
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 053e571b6285cd405ea17f43fec1d3ea99732070
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235587"
---
# <a name="what-is-the-azure-database-migration-service"></a>Что такое Azure Database Migration Service?
Azure Database Migration Service — это полностью управляемая служба, которая выполняет непрерывную миграцию из множества источников баз данных на платформы данных Azure с минимальным временем простоя (подключенные миграции).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Миграция баз данных в Azure с помощью привычных средств
Служба Azure Database Migration Service обладает некоторыми функциональными возможностями, которые можно найти в других наших средствах и службах. Она предоставляет пользователям комплексное решение с высокой доступностью. Служба использует [Помощник по миграции данных](https://aka.ms/dma) для создания экспертных отчетов с рекомендациями по изменениям, которые необходимы для переноса данных. Все необходимые исправления выполняются пользователями. Когда вы будете готовы приступить к переносу, Azure Database Migration Service выполнит все необходимые действия. Запустив перенос, вы можете больше не думать об этом процессе. Все будет сделано автоматически в соответствии с рекомендациями корпорации Майкрософт.

> [!NOTE]
> Используя Azure Database Migration Service, чтобы выполнить подключенную миграцию требуется создать экземпляр, который основан на ценовой категории критически важной для бизнеса (Предварительная версия).

## <a name="regional-availability"></a>Доступ по регионам
Azure Database Migration Service сейчас доступна в следующих регионах:

![Региональная доступность Azure Database Migration Service](media\overview\dms-regional-availability1.png)

Наиболее актуальные сведения о региональной доступности Azure Database Migration Service (на сайте глобальной инфраструктуры Azure) см. на странице [доступности продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="next-steps"></a>Дополнительная информация
- [Создание экземпляра службы Database Migration Service с помощью портала Azure](quickstart-create-data-migration-service-portal.md)
- [Миграция с SQL Server в базу данных SQL Azure](tutorial-sql-server-to-azure-sql.md).
- [Overview of prerequisites for using the Azure Database Migration Service](pre-reqs.md) (Предварительные требования для использования службы Azure Database Migration Service).
- [FAQ about using the Azure Database Migration Service](faq.md) (Часто задаваемые вопросы о службе Azure Database Migration Service).
