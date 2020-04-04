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
ms.openlocfilehash: cfb1b7558f8d7675009a0ebc729cc1a560f03d12
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633379"
---
# <a name="connect-to-synapse-sql-pool-with-sqlcmd"></a>Подключение к бассейну Synapse S'L с кв.м
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Машинное обучение Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

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
> 
> 

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
Для получения более подробной информации о вариантах, доступных в sqlcmd, [см.](https://msdn.microsoft.com/library/ms162773.aspx)
