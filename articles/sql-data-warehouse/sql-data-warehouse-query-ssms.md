---
title: Подключение с помощью SSMS
description: Подключайтесь к хранилищу данных SQL Azure и создавайте запросы к нему с помощью SQL Server Management Studio (SSMS).
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
ms.openlocfilehash: d5c903a24ea47cb152555330688dd0bc515c625b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692599"
---
# <a name="connect-to-sql-data-warehouse-with-sql-server-management-studio-ssms"></a>Подключение к хранилищу данных SQL Azure с помощью SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Машинное обучение Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Подключайтесь к хранилищу данных SQL Azure и создавайте запросы к нему с помощью SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим руководством необходимы указанные ниже компоненты.

* Существующее хранилище данных SQL. Чтобы создать его, см. раздел [Создание хранилища данных SQL][Create a SQL Data Warehouse].
* Установленный SQL Server Management Studio (SSMS). [Установите SSMS][Install SSMS] бесплатно, если у вас его еще нет.
* Полное имя сервера SQL Server. Чтобы найти это, см. раздел [Подключение к хранилищу данных SQL][Connect to SQL Data Warehouse].

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. подключение к хранилищу данных SQL
1. Откройте среду SSMS.
2. Откройте обозреватель объектов. Чтобы сделать это, выберите **Файл** > **Подключить к обозревателю объектов**.
   
    ![Обозреватель объектов SQL Server][1]
3. Заполните поля в окне «Подключение к серверу».
   
    ![Подключение к серверу][2]
   
   * **Имя сервера**. Введите найденное **имя сервера** .
   * **Проверка подлинности**. Выберите **Проверка подлинности SQL Server** или **Встроенная проверка подлинности Active Directory**.
   * **Имя пользователя** и **Пароль**. Если вы выбрали проверку подлинности SQL Server, введите имя пользователя и пароль.
   * Щелкните **Подключить**.
4. Чтобы исследовать данные, разверните сервер Azure SQL Server. Вы можете просмотреть базы данных, связанные с сервером. Разверните AdventureWorksDW, чтобы просмотреть таблицы в образце базы данных.
   
    ![Обзор AdventureWorksDW][3]

## <a name="2-run-a-sample-query"></a>2. Запуск примера запроса
Теперь, когда мы подключились к базе данных, давайте напишем запрос.

1. Щелкните правой кнопкой мыши базу данных в обозревателе объектов SQL Server.
2. Выберите пункт **Создать запрос**. Откроется окно нового запроса.
   
    ![Создать запрос][4]
3. Скопируйте следующий запрос TSQL в окно запроса.
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Выполните запрос. Для этого щелкните `Execute` или воспользуйтесь клавишами `F5`.
   
    ![Выполнение запроса][5]
5. Просмотрите результаты запроса. В этом примере таблица FactInternetSales содержит 60 398 строк.
   
    ![Результаты запроса][6]

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы можете подключаться к базе данных и отправлять запросы, попробуйте [визуализировать данные с помощью PowerBI][visualizing the data with PowerBI].

Сведения о настройке среды для проверки подлинности Azure Active Directory см. в статье Проверка подлинности [в хранилище данных SQL][Authenticate to SQL Data Warehouse].

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md 

<!--Other-->
[Azure portal]: https://portal.azure.com
[Install SSMS]: https://msdn.microsoft.com/library/hh213248.aspx


<!--Image references-->

[1]: media/sql-data-warehouse-query-ssms/connect-object-explorer.png
[2]: media/sql-data-warehouse-query-ssms/connect-object-explorer1.png
[3]: media/sql-data-warehouse-query-ssms/explore-tables.png
[4]: media/sql-data-warehouse-query-ssms/new-query.png
[5]: media/sql-data-warehouse-query-ssms/execute-query.png
[6]: media/sql-data-warehouse-query-ssms/results.png
