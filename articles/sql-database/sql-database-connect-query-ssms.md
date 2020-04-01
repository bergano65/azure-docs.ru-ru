---
title: SSMS. Подключение и запрос данных
description: Узнайте, как подключиться к базе данных SQL в Azure с помощью SQL Server Management Studio (SSMS). Затем выполните инструкции Transact-SQL (T-SQL) для запроса и изменения данных.
keywords: подключение к базе данных sql, sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/10/2020
ms.openlocfilehash: 31bd47128a272e75d7021180b536fe6bf7420f55
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79299300"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Краткое руководство. Подключение к базе данных SQL Azure и создание запросов к ней с помощью SQL Server Management Studio

Из этого краткого руководства вы узнаете, как использовать SQL Server Management Studio (SSMS) для подключения к Базе данных SQL Azure и выполнять запросы.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством требуется следующее:

- [SQL Server Management Studio (SSMS).](/sql/ssms/download-sql-server-management-studio-ssms/)
- Образец базы данных AdventureWorksLT. Если требуется рабочая копия базы данных AdventureWorksLT, создайте ее, выполнив действия из краткого руководства по [созданию базы данных SQL Azure](sql-database-single-database-get-started.md).
    - Скрипты в этой статье предназначены для использования базы данных AdventureWorksLT. Если вы используете управляемый экземпляр, необходимо импортировать базу данных AdventureWorks в базу данных экземпляра или изменить скрипты в этой статье для использования базы данных Wide World Importers.

Если вы просто хотите выполнить некоторые нерегламентированные запросы, не устанавливая SSMS, см. статью [Краткое руководство. Использование редактора запросов на портале Azure для запросов к базе данных SQL](sql-database-connect-query-portal.md).

## <a name="get-sql-server-connection-information"></a>Получение сведений о подключении к SQL Server

Получите сведения, необходимые для подключения к базе данных. Для выполнения инструкций из этого краткого руководства вам понадобится полное имя сервера или имя узла, имя базы данных и данные для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).

2. Перейдите на страницу **База данных SQL** или **Управляемые экземпляры SQL**, к которой хотите отправить запрос.

3. На странице **Обзор** скопируйте полное имя сервера. Оно расположено рядом с полем **Имя сервера** для отдельной базы данных. Или же это полное имя сервера рядом с полем **Узел** для управляемого экземпляра. Полное имя выглядит следующим образом: *servername.database.windows.net* (за исключением фактического имени вашего сервера).

## <a name="connect-to-your-database"></a>Подключение к базе данных

В SMSS подключитесь к своему серверу Базы данных SQL Azure.

> [!IMPORTANT]
> Сервер Базы данных SQL Azure прослушивает порт 1433. Чтобы подключиться к серверу Базы данных SQL при включенном корпоративном брандмауэре, необходимо открыть этот порт в брандмауэре.

1. Откройте SSMS.

2. Откроется диалоговое окно **Соединение с сервером** . Введите следующие сведения:

   | Параметр      | Рекомендуемое значение    | Описание |
   | ------------ | ------------------ | ----------- |
   | **Тип сервера** | Ядро СУБД | Обязательное значение. |
   | **Имя сервера** | Полное имя сервера | Что-то вроде: **имя_сервера.база_данных.windows.net**. |
   | **Аутентификация** | Проверка подлинности SQL Server | В этом руководстве используется проверка подлинности SQL. |
   | **Имя входа** | Идентификатор пользователя учетной записи администратора сервера | Идентификатор пользователя учетной записи администратора сервера, используемый для создания сервера. |
   | **Пароль** | Пароль для учетной записи администратора сервера | Пароль учетной записи администратора сервера, используемый для создания сервера. |
   ||||

   ![подключение к серверу](./media/sql-database-connect-query-ssms/connect.png)  

3. Выберите **Параметры** в диалоговом окне **Подключение к серверу**. В раскрывающемся меню **Подключение к базе данных** выберите **mySampleDatabase**. После выполнения [предварительных требований](#prerequisites) краткого руководства у вас будет база данных AdventureWorksLT с именем mySampleDatabase. Если рабочая копия базы данных AdventureWorks имеет имя, отличное от mySampleDatabase, выберите ее.

   ![Подключение к базе данных на сервере](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Выберите **Подключиться**. После этого откроется окно обозревателя объектов.

5. Чтобы просмотреть объекты базы данных, разверните раздел **Базы данных**, а затем узел своей базы данных.

   ![Объекты mySampleDatabase](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Данные запросов

Запустите следующий код Transact-SQL [SELECT](https://msdn.microsoft.com/library/ms189499.aspx), чтобы запросить за категорией 20 основных продуктов.

1. В обозревателе объектов щелкните правой кнопкой мыши **mySampleDatabase** и выберите **Новый запрос**. После этого откроется новое окно запроса, подключенное к базе данных.

2. В окне запроса вставьте следующий SQL-запрос:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. На панели инструментов выберите команду **Выполнить**, чтобы выполнить запрос и извлечь данные из таблиц `Product` и `ProductCategory`.

    ![запрос для получения данных из таблицы Product и ProductCategory](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>Добавление данных

Запустите следующий код Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx), чтобы добавить новый продукт в таблицу `SalesLT.Product`.

1. Замените предыдущий запрос следующим содержимым.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Щелкните **Выполнить**, чтобы вставить новую строку в таблицу `Product`. В области **Сообщения** отображается текст: **(1 затронутая строка)**

#### <a name="view-the-result"></a>Просмотр результата

1. Замените предыдущий запрос следующим содержимым.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Нажмите кнопку **Выполнить**. Отобразится такой результат.

   ![результат запроса к таблице Product](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>Обновление данных

Запустите код Transact-SQL [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx), чтобы обновить новый продукт.

1. Замените предыдущий запрос текущим, который возвращает созданную ранее запись:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Щелкните **Выполнить**, чтобы обновить указанную строку в таблице `Product`. В области **Сообщения** отображается текст: **(1 затронутая строка)**

### <a name="delete-data"></a>Удаление данных

Запустите код Transact-SQL [DELETE](https://msdn.microsoft.com/library/ms189835.aspx), чтобы удалить новый продукт.

1. Замените предыдущий запрос следующим содержимым.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Щелкните **Выполнить**, чтобы удалить указанную строку в таблице `Product`. В области **Сообщения** отображается текст: **(1 затронутая строка)**

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения об использовании SSMS см. в [этой статье](https://msdn.microsoft.com/library/ms174173.aspx).
- Чтобы подключиться и выполнять запросы при помощи портала Azure, прочитайте статью [Портал Azure. Использование редактора SQL-запросов для подключения к данным и их запроса](sql-database-connect-query-portal.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Visual Studio Code](sql-database-connect-query-vscode.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью .NET](sql-database-connect-query-dotnet.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью PHP](sql-database-connect-query-php.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Node.js](sql-database-connect-query-nodejs.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Java](sql-database-connect-query-java.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Python](sql-database-connect-query-python.md).
- См. дополнительные сведения о [подключении и создании запросов с помощью Ruby](sql-database-connect-query-ruby.md).
