---
title: База данных SQL Azure — Преимущество гибридного использования Azure | Документация Майкрософт
description: Используйте существующие лицензии SQL Server для скидок базы данных SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 10/08/2019
ms.openlocfilehash: cb5caf65a3083cfb5700432573cfddcfb7c6385f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515893"
---
# <a name="azure-hybrid-benefit"></a>Преимущество гибридного использования Azure

В подготовленной среде вычислений для модели приобретения на основе виртуальное ядро вы можете обмениваться существующими лицензиями по тарифам со скидкой в базе данных SQL с помощью [преимущество гибридного использования Azure для SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Это преимущество Azure позволяет сэкономить до 30% в базе данных SQL Azure, используя локальные лицензии SQL Server с Software Assurance.

![цены](./media/sql-database-service-tiers/pricing.png)


## <a name="choose-a-license-model"></a>Выбор модели лицензии

С помощью Преимущество гибридного использования Azure вы можете платить только за базовую инфраструктуру Azure, используя существующую лицензию SQL Server для самого ядра СУБД SQL (цены на базовые вычисления) или платить за базовую инфраструктуру и SQL Server Лицензия (цены, включаемые в лицензию).

Вы можете выбрать или изменить модель лицензирования с помощью портал Azure или с помощью одного из следующих API:

- Чтобы задать или обновить тип лицензии с помощью PowerShell, выполните следующие действия.

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Чтобы задать или обновить тип лицензии с помощью Azure CLI:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Чтобы задать или обновить тип лицензии с помощью REST API:

  - [Базы данных: создание или обновление](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Базы данных: обновление](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Управляемые экземпляры. Создание или обновление](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Управляемые экземпляры. Обновление](https://docs.microsoft.com/rest/api/sql/managedinstances/update)



## <a name="next-steps"></a>Дальнейшие действия

- Сведения о выборе между вариантами развертывания базы данных SQL см. [в разделе Выбор правильного развертывания в SQL Azure](sql-database-paas-vs-sql-server-iaas.md).
- Сравнение функций базы данных SQL см. в статье [функции базы данных SQL Azure](sql-database-features.md).
