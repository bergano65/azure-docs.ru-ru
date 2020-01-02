---
title: Преимущество гибридного использования Azure
description: Используйте существующие лицензии SQL Server для скидок базы данных SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: 394c674da63bbda643246c3d61fb670d6ac8d1f7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928613"
---
# <a name="azure-hybrid-benefit"></a>Преимущество гибридного использования Azure

В подготовленной среде вычислений для модели приобретения на основе виртуальное ядро вы можете обмениваться существующими лицензиями по тарифам со скидкой в базе данных SQL с помощью [преимущество гибридного использования Azure для SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Это преимущество Azure позволяет сэкономить до 30% в базе данных SQL Azure, используя локальные лицензии SQL Server с Software Assurance. 

> [!NOTE]
> Переход на Преимущество гибридного использования Azure не требует времени простоя.

![цены](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Выбор модели лицензии

С помощью Преимущество гибридного использования Azure вы можете платить только за базовую инфраструктуру Azure, используя существующую лицензию SQL Server для самого ядра СУБД SQL (цены на базовые вычисления) или платить за базовую инфраструктуру и SQL Server Лицензия (цены, включаемые в лицензию).

Вы можете выбрать или изменить модель лицензирования с помощью портал Azure или с помощью одного из следующих API:

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы задать или обновить тип лицензии с помощью PowerShell, выполните следующие действия.

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы задать или обновить тип лицензии с помощью Azure CLI:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-apitabrest"></a>[REST API](#tab/rest)

Чтобы задать или обновить тип лицензии с помощью REST API:

- [Создание или обновление баз данных](/rest/api/sql/databases/createorupdate)
- [Базы данных: обновление](/rest/api/sql/databases/update)
- [Управляемые экземпляры. Создание или обновление](/rest/api/sql/managedinstances/createorupdate)
- [Управляемые экземпляры. Обновление](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о выборе между вариантами развертывания базы данных SQL см. [в разделе Выбор правильного развертывания в SQL Azure](sql-database-paas-vs-sql-server-iaas.md).
- Сравнение функций базы данных SQL см. в статье [функции базы данных SQL Azure](sql-database-features.md).
