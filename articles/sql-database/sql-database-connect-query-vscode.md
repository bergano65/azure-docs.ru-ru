---
title: VS Code. Подключение и запрос данных в Базе данных SQL Azure | Документация Майкрософт
description: Сведения о подключении к базе данных SQL в Azure с помощью Visual Studio Code и выполнении инструкций Transact-SQL (T-SQL) для запроса и изменения данных.
keywords: подключение к базе данных SQL
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/12/2019
ms.openlocfilehash: 669da18c889d906c629a7656eaa766993bf67373
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238357"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query-an-azure-sql-database"></a>Краткое руководство. Подключение и запрос Базы данных SQL Azure с помощью Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com/docs) — это графический редактор кода для Linux, macOS и Windows. Он поддерживает расширения, включая [расширение mssql](https://aka.ms/mssql-marketplace), для выполнения запросов к Microsoft SQL Server, Базе данных SQL Azure и Хранилищу данных SQL. В этом кратком руководстве показано использование Visual Studio Code для подключения к базе данных SQL Azure, а также выполнение запроса, вставки, обновления и удаления данных с помощью инструкций Transact-SQL.

## <a name="prerequisites"></a>Предварительные требования

- База данных SQL Azure. Для создания и настройки базы данных в службе "База данных SQL Azure" можно использовать одно из этих кратких руководств.

  || Отдельная база данных | Управляемый экземпляр |
  |:--- |:--- |:---|
  | Создание| [Портал](sql-database-single-database-get-started.md) | [Портал](sql-database-managed-instance-get-started.md) |
  || [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](scripts/sql-database-create-and-configure-database-cli.md) | [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) |
  | Настройка | [Правило брандмауэра IP-адресов на уровне сервера](sql-database-server-level-firewall-rule.md)| [Подключение из виртуальной машины](sql-database-managed-instance-configure-vm.md)|
  |||[Подключение "точка — сеть"](sql-database-managed-instance-configure-p2s.md)
  |Загрузка данных|База данных Adventure Works, загруженная для краткого руководства|[Восстановление базы данных Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Восстановление или импорт Adventure Works из файла [BACPAC](sql-database-import.md), размещенного на [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Скрипты в этой статье предназначены для использования базы данных Adventure Works. Используя управляемый экземпляр, необходимо импортировать базу данных Adventure Works в базу данных экземпляра или изменить скрипты в этой статье для использования базы данных Wide World Importers.

## <a name="install-visual-studio-code"></a>Установка Visual Studio Code

Убедитесь, что установили последнюю версию [Visual Studio Code](https://code.visualstudio.com/Download) и загрузили [расширение mssql](https://aka.ms/mssql-marketplace). Руководство по установке расширения mssql см. в разделе [об установке VS Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) и на странице [расширения mssql для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql).

## <a name="configure-visual-studio-code"></a>Откройте Visual Studio Code. 

### <a name="mac-os"></a>**Mac OS**

Для macOS необходимо установить OpenSSL. Это предварительное требование для платформы .NET Core, используемой для расширения mssql. Откройте терминал и введите следующие команды для установки **brew** и **OpenSSL**. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Специальная настройка не требуется.

### <a name="windows"></a>**Windows**

Специальная настройка не требуется.

## <a name="get-sql-server-connection-information"></a>Получение сведений о подключении к SQL Server

Получите сведения, необходимые для подключения к базе данных SQL Azure. Для дальнейших действий вам понадобится полное имя сервера или имя узла, имя базы данных и данные для входа.

1. Войдите на [портале Azure](https://portal.azure.com/).

2. Перейдите на страницу **Базы данных SQL** или **Управляемые экземпляры SQL**.

3. На странице **Обзор** просмотрите полное имя сервера рядом с полем **Имя сервера** для отдельной базы данных или полное имя сервера рядом с полем **Узел** для управляемого экземпляра. Чтобы скопировать имя сервера или имя узла, наведите на него указатель мыши и щелкните значок **копирования**.

## <a name="set-language-mode-to-sql"></a>Выбор режима языка SQL

В Visual Studio Code укажите для режима языка значение **SQL**, чтобы активировать команды mssql и T-SQL IntelliSense.

1. Откройте новое окно Visual Studio Code. 

2. Нажмите клавиши **CTRL**+**N**. Откроется новый обычный текстовый файл. 

3. В правом нижнем углу строки состояния выберите **Обычный текст**.

4. В открывшемся раскрывающемся меню **Выберите языковой режим** выберите **SQL**. 

## <a name="connect-to-your-database"></a>Подключение к базе данных

С помощью Visual Studio Code подключитесь к серверу базы данных SQL Azure.

> [!IMPORTANT]
> Прежде чем продолжить, приготовьте сервер и данные для входа. Если при вводе данных профиля подключения переключиться с Visual Studio Code, понадобится начать создание профиля заново.
>

1. В Visual Studio Code нажмите клавиши **CTRL+SHIFT+P** (или **F1**), чтобы открыть палитру команд.

2. Выберите **MS SQL:Connect** и нажмите клавишу **ВВОД**.

3. Выберите **Create Connection Profile** (Создать профиль подключения).

4. Следуйте инструкциям на экране, чтобы указать свойства для нового профиля подключения. Укажите все значения и нажмите клавишу **ВВОД** для продолжения. 

   | Свойство       | Рекомендуемое значение | ОПИСАНИЕ |
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Server name** (Имя сервера) | Полное имя сервера | Примерно такое: **mynewserver20170313.database.windows.net**. |
   | **Database name** (Имя базы данных) | mySampleDatabase | База данных для подключения. |
   | **Проверка подлинности** | Имя для входа в SQL| В этом руководстве используется проверка подлинности SQL. |
   | **Имя пользователя** | Имя пользователя | Имя пользователя учетной записи администратора сервера, используемое для создания сервера. |
   | **Password (SQL Login)** (Пароль для входа в SQL) | Пароль | Пароль учетной записи администратора сервера, используемый для создания сервера. |
   | **Save Password?** (Сохранить пароль?) | "Да" или "Нет" | Выберите **Да**, если вы не хотите вводить пароль каждый раз. |
   | **Укажите имя для этого профиля** | Имя профиля, например **mySampleProfile** | Сохраненный профиль повышает скорость подключения при последующих входах. | 

   В случае успешного выполнения появится уведомление о том, что профиль создан и подключен.

## <a name="query-data"></a>Запрос данных

Выполните указанную нижу инструкцию Transact-SQL [SELECT](https://msdn.microsoft.com/library/ms189499.aspx), чтобы запросить 20 основных продуктов из категории.

1. В окне редактора вставьте следующий запрос SQL.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Нажмите клавиши **CTRL**+**SHIFT**+**E** для выполнения запроса и отображения результатов из таблиц `Product` и `ProductCategory`.

    ![Запрос для получения данных из 2 таблиц](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Добавление данных

Выполните указанную ниже инструкцию Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx), чтобы добавить новый продукт в таблицу `SalesLT.Product`.

1. Замените предыдущий запрос следующим содержимым.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Нажмите клавиши **CTRL**+**SHIFT**+**E** для вставки новой строки в таблицу `Product`.

## <a name="update-data"></a>Обновление данных

Выполните указанную ниже инструкцию Transact-SQL [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx), чтобы обновить добавленный продукт.

1. Замените предыдущий запрос следующим содержимым:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Нажмите клавиши **CTRL**+**SHIFT**+**E**, чтобы обновить указанную строку в таблице `Product`.

## <a name="delete-data"></a>Удаление данных

Выполните указанную ниже инструкцию Transact-SQL [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql), чтобы удалить новый продукт.

1. Замените предыдущий запрос следующим содержимым:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Нажмите клавиши **CTRL**+**SHIFT**+**E**, чтобы удалить указанную строку в таблице `Product`.

## <a name="next-steps"></a>Дополнительная информация

- Сведения о подключении и отправке запросов с помощью SQL Server Management Studio см. в статье [Краткое руководство. База данных SQL Azure: подключение к базе данных Azure SQL и создание запросов к ней с помощью SQL Server Management Studio](sql-database-connect-query-ssms.md).
- Сведения о подключении и отправке запросов с помощью портала Azure см. в статье [Краткое руководство. Портал Azure: использование редактора SQL-запросов для подключения к данным и создания запросов к данным](sql-database-connect-query-portal.md).
- Статью из журнала MSDN, посвященную использованию кода Visual Studio, можно просмотреть в записи блога о [создании базы данных IDE с помощью расширения MSSQL](https://msdn.microsoft.com/magazine/mt809115).
