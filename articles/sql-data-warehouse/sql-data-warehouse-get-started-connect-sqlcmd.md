---
title: Подключение с помощью sqlcmd
description: Подключайтесь к хранилищу данных SQL Azure и создавайте запросы к нему с помощью служебной программы командной строки sqlcmd.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e4b432e0be0cdded5089965b9d272aa82e31bd36
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685739"
---
# <a name="connect-to-sql-data-warehouse-with-sqlcmd"></a>Подключение к хранилищу данных SQL с помощью sqlcmd
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Машинное обучение Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Используйте программу командной строки [sqlcmd][sqlcmd] для подключения к хранилищу данных SQL Azure и выполнения запросов к нему.  

## <a name="1-connect"></a>1. подключение
Чтобы начать использовать [sqlcmd][sqlcmd], откройте командную строку и введите **sqlcmd** и строку подключения к базе данных хранилища данных SQL. В строке подключения обязательно укажите следующие параметры.

* **Server (-S)**  — сервер в формате `<`имя_сервера`>`.database.windows.net
* **Database (-D)**  — имя базы данных.
* **Enable Quoted Identifiers (-I)** — для подключения к экземпляру хранилища данных SQL необходимо включить заключенные в кавычки идентификаторы.

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

## <a name="2-query"></a>2. запрос
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
Дополнительные сведения о параметрах, доступных в программе sqlcmd, см. в [документации по sqlcmd][sqlcmd] .

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
