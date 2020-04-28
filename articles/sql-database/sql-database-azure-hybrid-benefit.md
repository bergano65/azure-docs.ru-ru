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
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75945622"
---
# <a name="azure-hybrid-benefit"></a>Преимущество гибридного использования Azure

В подготовленной среде вычислений для модели приобретения на основе виртуальное ядро вы можете обмениваться существующими лицензиями по тарифам со скидкой в базе данных SQL с помощью [преимущество гибридного использования Azure для SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Это преимущество Azure позволяет сэкономить до 30% или даже выше в базе данных SQL Azure с помощью локальных лицензий SQL Server с Software Assurance. Используйте калькулятор Преимущество гибридного использования Azure, используя ссылку, указанную ранее для правильных значений. 

> [!NOTE]
> Переход на Преимущество гибридного использования Azure не требует времени простоя.

![цены](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Выбор модели лицензии

С помощью Преимущество гибридного использования Azure вы можете платить только за базовую инфраструктуру Azure, используя существующую лицензию SQL Server для самого ядра СУБД SQL (цены на базовое вычисление) или платить за базовую инфраструктуру и лицензию на SQL Server (цены, включаемые в лицензию).

Вы можете выбрать или изменить модель лицензирования с помощью портал Azure или с помощью одного из следующих API:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы задать или обновить тип лицензии с помощью PowerShell, выполните следующие действия.

- [New-Азсклдатабасе](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы задать или обновить тип лицензии с помощью Azure CLI:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST API](#tab/rest)

Чтобы задать или обновить тип лицензии с помощью REST API:

- [Базы данных: создание или обновление](/rest/api/sql/databases/createorupdate)
- [Базы данных: обновление](/rest/api/sql/databases/update)
- [Управляемые экземпляры. Создание или обновление](/rest/api/sql/managedinstances/createorupdate)
- [Управляемые экземпляры. Обновление](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>Следующие шаги

- Сведения о выборе между вариантами развертывания базы данных SQL см. [в разделе Выбор правильного развертывания в SQL Azure](sql-database-paas-vs-sql-server-iaas.md).
- Сравнение функций базы данных SQL см. в статье [функции базы данных SQL Azure](sql-database-features.md).
