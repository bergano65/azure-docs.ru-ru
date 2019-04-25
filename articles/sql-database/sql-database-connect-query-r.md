---
title: Использование R для выполнения запросов к Базе данных SQL Azure
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: В этой статье показано, как использовать сценарий R для подключения к Базе данных SQL Azure и выполнения запросов к ней с помощью инструкций Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 2b1206e3087b0573736174d4eed502653d76f7a5
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001195"
---
# <a name="quickstart-use-r-to-query-an-azure-sql-database-preview"></a>Краткое руководство. Использование R для выполнения запросов к Базе данных SQL Azure (предварительная версия)

 В этом кратком руководстве показано, как использовать [R](https://www.r-project.org/) и Службы машинного обучения для подключения к Базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос данных. Службы машинного обучения — это функция Базы данных SQL Azure, которая используется для выполнения скриптов R в базе данных. Дополнительные сведения см. в разделе [Azure SQL Database Machine Learning Services with R (preview)](sql-database-machine-learning-services-overview.md) (Службы машинного обучения (с поддержкой R) в Базе данных SQL Azure (предварительная версия)).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Ниже указаны требования для работы с этим кратким руководством.

- База данных SQL Azure. Для создания и настройки базы данных в службе "База данных SQL Azure" можно использовать одно из этих кратких руководств.

  || Отдельная база данных | Управляемый экземпляр |
  |:--- |:--- |:---|
  | Создание| [Портал](sql-database-single-database-get-started.md) | [Портал](sql-database-managed-instance-get-started.md) |
  || [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](scripts/sql-database-create-and-configure-database-cli.md) | [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Настройка | [Правило брандмауэра IP-адресов на уровне сервера](sql-database-server-level-firewall-rule.md)| [Подключение из виртуальной машины](sql-database-managed-instance-configure-vm.md)|
  |||[Подключение "точка — сеть"](sql-database-managed-instance-configure-p2s.md)
  |||

- Службы машинного обучения (с поддержкой R) включены. Во время использования общедоступной предварительной версии корпорация Майкрософт подключит вас и включит машинное обучение для имеющейся или новой базы данных. Выполните шаги, приведенные в разделе [Sign up for the preview](sql-database-machine-learning-services-overview.md#signup) (Регистрация для получения предварительной версии).

- Последняя версия [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Вы можете выполнять сценарии R с помощью других инструментов управления базами данных или выполнения запросов, но в этом кратком руководстве вы будете использовать SSMS.

## <a name="get-sql-server-connection-information"></a>Получение сведений о подключении к SQL Server

Получите сведения, необходимые для подключения к базе данных SQL Azure. Для дальнейших действий вам понадобится полное имя сервера или имя узла, имя базы данных и данные для входа.

1. Войдите на [портале Azure](https://portal.azure.com/).

2. Перейдите на страницу **Базы данных SQL** или **Управляемые экземпляры SQL**.

3. На странице **Обзор** просмотрите полное имя сервера рядом с полем **Имя сервера** для отдельной базы данных или полное имя сервера рядом с полем **Узел** для управляемого экземпляра. Чтобы скопировать имя сервера или имя узла, наведите на него указатель мыши и щелкните значок **копирования**.

## <a name="create-code-to-query-your-sql-database"></a>Создание кода для запроса базы данных SQL

1. Откройте **SQL Server Management Studio** и подключитесь к базе данных SQL.

   Если вам нужна помощь с подключением, ознакомьтесь с разделом [Краткое руководство. Подключение к базе данных SQL Azure и создание запросов к ней с помощью SQL Server Management Studio](sql-database-connect-query-ssms.md).

1. Передайте полный сценарий R в хранимую процедуру [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

   Для этого используется аргумент `@script`. Все внутри аргумента `@script` должно быть допустимым кодом R.

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Если вы получаете какие-либо ошибки, это может быть связано с тем, что общедоступная предварительная версия Служб машинного обучения (с использованием R) не включена для базы данных SQL. См. [предварительные требования](#prerequisites) выше.

## <a name="run-the-code"></a>Выполнение кода

1. Выполните хранимую процедуру [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

1. В окне **Сообщения**  убедитесь, что возвращены первые 20 строк с продуктами из категории.

## <a name="next-steps"></a>Дополнительная информация

- [Проектирование первой базы данных SQL Azure](sql-database-design-first-database.md)
- [Machine Learning Services (with R) in Azure SQL Database (preview)](sql-database-machine-learning-services-overview.md) (Службы машинного обучения (с использованием R) в Базе данных SQL Azure (предварительная версия))
- [Создание и выполнение простых сценариев R в Службах машинного обучения в Базе данных SQL Azure (предварительная версия)](sql-database-quickstart-r-create-script.md)
- [Запись расширенных функций R в Базу данных SQL Azure с помощью Служб машинного обучения (предварительная версия)](sql-database-machine-learning-services-functions.md)
