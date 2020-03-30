---
title: Преимущество гибридного использования Azure
description: Используйте существующие лицензии S'L Server для скидок базы данных S'L.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945622"
---
# <a name="azure-hybrid-benefit"></a>Преимущество гибридного использования Azure

В подготовленном вычислительном уровне модели закупок на основе vCore можно обменять существующие лицензии на льготные ставки на базе данных S'L, используя [Azure Hybrid Benefit для сервера S'L.](https://azure.microsoft.com/pricing/hybrid-benefit/) Это преимущество Azure позволяет экономить до 30 процентов или даже выше на базе данных Azure S'L, используя свои внутренние лицензии S'L Server с software Assurance. Пожалуйста, используйте калькулятор гибридных льгот Azure, используя ссылку, упомянутую ранее, для правильных значений. 

> [!NOTE]
> Изменение в Azure Hybrid Benefit не требует простоев.

![цены](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Выбрать модель лицензии

С помощью Azure Hybrid Benefit вы можете платить только за базовую инфраструктуру Azure, используя существующую лицензию S'L Server для самого движка базы данных S'L (базовые цены на вычислительные данные), или вы можете оплатить как базовую инфраструктуру, так и сервер S'L лицензия (Лицензия-включенное ценообразование).

Вы можете выбрать или изменить модель лицензирования с помощью портала Azure или с помощью одного из следующих AI:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Для настройки или обновления типа лицензии с помощью PowerShell:

- [Новый-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

Для настройки или обновления типа лицензии с помощью Azure CLI:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST API](#tab/rest)

Для настройки или обновления типа лицензии с помощью REST API:

- [Базы данных: создание или обновление](/rest/api/sql/databases/createorupdate)
- [Базы данных: обновление](/rest/api/sql/databases/update)
- [Управляемые экземпляры. Создание или обновление](/rest/api/sql/managedinstances/createorupdate)
- [Управляемые экземпляры. Обновление](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>Дальнейшие действия

- При выборе между вариантами развертывания базы данных S'L [см.](sql-database-paas-vs-sql-server-iaas.md)
- Для сравнения функций базы данных S'L [см.](sql-database-features.md)
