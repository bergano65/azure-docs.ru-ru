---
title: Загрузка данных из CSV-файла в базу данных SQL Azure (BCP)
description: Для импорта небольших объемов данных в базу данных SQL Azure используйте программу bcp.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: d9c4a6293fe2c52870e61488713455ac6c2ed4a1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689566"
---
# <a name="load-data-from-csv-into-azure-sql-database-flat-files"></a>Загрузка данных из CSV-файла в базу данных SQL Azure (неструктурированные файлы)

Для импорта данных из CSV-файла в Базу данных SQL Azure можно использовать программу командной строки bcp.

## <a name="before-you-begin"></a>Перед началом работы

### <a name="prerequisites"></a>Предварительные требования

Для выполнения задач из этой статьи необходимо следующее:

* База данных и сервер Базы данных SQL Azure
* установленная служебная программа командной строки bcp;
* установленная служебная программа командной строки sqlcmd.

Вы можете загрузить служебные программы bcp и sqlcmd в [Центре загрузки Майкрософт][Microsoft Download Center].

### <a name="data-in-ascii-or-utf-16-format"></a>Данные в формате ASCII или UTF-16

Чтобы выполнить действия, описанные в этом руководстве, необходимо использовать данные в формате ASCII или UTF-16, так как bcp не поддерживает кодировку UTF-8. 

## <a name="1-create-a-destination-table"></a>1. Создание целевой таблицы

Определите таблицу в базе данных SQL как целевую таблицу. Столбцы в таблице должны соответствовать данным в каждой строке файла данных.

Чтобы создать таблицу, откройте окно командной строки и используйте sqlcmd.exe для выполнения следующей команды:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```


## <a name="2-create-a-source-data-file"></a>2. Создание файла исходных данных

Откройте блокнот и скопируйте следующие строки данных в новый текстовый файл, а затем сохраните этот файл в локальный временный каталог (C:\Temp\DimDate2.txt). Эти данные имеют формат ASCII.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

Чтобы экспортировать данные из базы данных SQL Server, откройте окно командной строки и выполните команду ниже (необязательно). Замените значения TableName, ServerName, DatabaseName, Username и Password на собственные.

```bcp
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t , 
```

## <a name="3-load-the-data"></a>3. Загрузка данных

Чтобы загрузить данные, откройте окно командной строки и выполните следующую команду, подставив собственные значения имени сервера, базы данных, пользователя и пароль.

```bcp
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

Используйте команду ниже, чтобы убедиться, что данные загружены правильно.

```bcp
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Результат должен выглядеть следующим образом:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4\. |
| 20150501 |2 |4\. |
| 20150601 |2 |4\. |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4\. |2 |
| 20151101 |4\. |2 |
| 20151201 |4\. |2 |

## <a name="next-steps"></a>Дальнейшие действия

Миграция базы данных SQL Server описана в статье [Миграция базы данных SQL Server в базу данных SQL в облаке](sql-database-single-database-migrate.md).

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
