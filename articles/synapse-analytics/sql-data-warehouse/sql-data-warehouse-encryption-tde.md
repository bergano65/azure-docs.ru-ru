---
title: Прозрачное шифрование данных (Портал)
description: Прозрачное шифрование данных (TDE) в аналитике Azure Synapse
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 247691326e3aa2c8027dd0318b23a2cbfcba1efe
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745224"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Начало работы с прозрачным шифрованием данных (TDE)

> [!div class="op_single_selector"]
>
> * [Обзор безопасности](sql-data-warehouse-overview-manage-security.md)
> * [Проверка подлинности](sql-data-warehouse-authentication.md)
> * [Шифрование (портал)](sql-data-warehouse-encryption-tde.md)
> * [Шифрование (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Необходимые разрешения

Чтобы включить прозрачное шифрование данных, необходимо иметь права администратора или участника роли dbmanager.

## <a name="enabling-encryption"></a>Включение шифрования

Для включения TDE выполните ниже:

1. Откройте базу данных на [портале Azure](https://portal.azure.com)
2. В колонке базы данных нажмите кнопку **Параметры** .
3. Выберите параметры ![портала **системы шифрования данных Transparent**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Выберите настройки ![портала **настройки на**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. Выберите параметры портала **Сохранить**
   ![](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>Отключение шифрования

Чтобы отключить TDE, выполните ниже:

1. Откройте базу данных на [портале Azure](https://portal.azure.com)
2. В колонке базы данных нажмите кнопку **Параметры** .
3. Выберите параметры ![портала **системы шифрования данных Transparent**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. Выберите настройки ![ **портала Off**](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. Выберите **Настройка портала Сохранить**
   ![2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>Динамические административные представления шифрования

Шифрование может быть подтверждено с помощью следующих динамических административных представлений.

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
