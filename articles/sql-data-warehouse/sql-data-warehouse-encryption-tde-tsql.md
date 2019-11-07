---
title: Прозрачное шифрование данных (T-SQL)
description: Прозрачное шифрование данных в хранилище данных SQL (T-SQL)
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 98695aa7578e90be9ada2f86640e68b05a9812ed
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692748"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Начало работы с прозрачным шифрованием данных (TDE)
> [!div class="op_single_selector"]
> * [Обзор безопасности](sql-data-warehouse-overview-manage-security.md)
> * [Проверка подлинности](sql-data-warehouse-authentication.md)
> * [Шифрование (портал)](sql-data-warehouse-encryption-tde.md)
> * [Шифрование (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Необходимые разрешения
Чтобы включить прозрачное шифрование данных, необходимо иметь права администратора или участника роли dbmanager.

## <a name="enabling-encryption"></a>Включение шифрования
Чтобы включить прозрачное шифрование данных для хранилища данных SQL, сделайте следующее.

1. Подключитесь к *главной* базе данных на сервере, где находится база данных, указав логин администратора или члена роли **dbmanager** в главной базе данных.
2. Выполните следующий оператор для шифрования базы данных:

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Отключение шифрования
Чтобы отключить прозрачное шифрование данных для хранилища данных SQL, сделайте следующее.

1. Подключитесь к *главной* базе данных, указав логин администратора или члена роли **dbmanager** в главной базе данных.
2. Выполните следующий оператор для шифрования базы данных:

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Прежде чем вносить изменения в параметры прозрачного шифрования данных, работу приостановленного хранилища данных SQL нужно возобновить.
> 
> 

## <a name="verifying-encryption"></a>Проверка шифрования
Чтобы проверить состояние шифрования для хранилища данных SQL, выполните указанные ниже действия.

1. Подключитесь к *главной* базе данных или к базе данных экземпляра, указав логин администратора или члена роли **dbmanager** в главной базе данных.
2. Выполните следующий оператор для шифрования базы данных:

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Результат ```1``` означает зашифрованную, а ```0``` — незашифрованную базу данных.

## <a name="encryption-dmvs"></a>Динамические административные представления шифрования
* [sys.databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
