---
title: Связь с sqlcmd
description: Используйте утилиту командной строки sqlcmd для подключения и запроса пула Synapse S'L.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 02157ca0d32d2347e50cc84a5c52e9c47b0f33b5
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745205"
---
# <a name="connect-to-synapse-sql-pool-with-sqlcmd"></a>Подключение к бассейну Synapse S'L с кв.м

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Машинное обучение Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Используйте командную линию «sqlcmd» для подключения к пулу S'L и запроса.  

## <a name="1-connect"></a>1. Подключение

Чтобы начать работу с «sqlcmd» (sqlcmd), откройте запрос команды и введите **sqlcmd** с последующей строкой подключения для базы данных пула S'L. В строке подключения обязательно укажите следующие параметры.

* **Server (-S)**  — сервер в формате `<`имя_сервера`>`.database.windows.net
* **База данных (-d):** Имя базы данных.
* **Включить цитируемые идентификаторы (-I):** Цитируемые идентификаторы должны быть включены для подключения к экземпляру пула S'L.

Чтобы использовать проверку подлинности SQL Server, необходимо добавить параметры имени пользователя и пароля.

* **User (-U)**  — пользователь сервера в формате `<`Пользователь`>`.
* **Password (-P)** — пароль, связанный с пользователем.

Например, строка подключения может выглядеть так:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Чтобы использовать встроенную проверку подлинности Azure Active Directory, необходимо добавить параметры Azure Active Directory.

* **Проверки подлинности Azure Active Directory (-G):** — использовать Azure Active Directory для проверки подлинности.

Например, строка подключения может выглядеть так:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Необходимо [включить проверку подлинности Azure Active Directory](sql-data-warehouse-authentication.md) , чтобы выполнять проверку подлинности с помощью Active Directory.

## <a name="2-query"></a>2. Запрос

После подключения можно подавать любые поддерживаемые инструкции Transact-SQL для экземпляра.  В этом примере запросы отправляются в интерактивном режиме.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

В следующих примерах показано, как выполнить запросы в пакетном режиме, используя параметр -Q или передав SQL программе sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Дальнейшие действия

Для получения более подробной информации о вариантах, доступных в sqlcmd, [см.](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
