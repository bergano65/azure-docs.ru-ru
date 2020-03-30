---
title: Подключение к SSMS
description: Для подключения и запроса Azure Synapse Analytics и запроса используйте студию управления серверами (SSMS).
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
ms.openlocfilehash: 12f8240d254b2d393734604928a809a2d9f1e14e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351644"
---
# <a name="connect-to-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>Подключитесь к аналитике Azure Synapse с помощью студии управления серверами S'L (SSMS)
> [!div class="op_single_selector"]
> * [Мощность BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Машинное обучение Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Для подключения и запроса хранилища данных в Azure Synapse и запроса хранилища данных в Azure Synapse и спользуйте S'L Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим руководством необходимы указанные ниже компоненты.

* Существующий пул S'L. Чтобы создать его, [см.](create-data-warehouse-portal.md)
* Установленный SQL Server Management Studio (SSMS). [Установите SSMS](https://msdn.microsoft.com/library/hh213248.aspx) бесплатно, если вы еще этого не сделали.
* Полное имя сервера SQL Server. Чтобы найти эту информацию, [перейдите на номер Connect to s-L.](sql-data-warehouse-connect-overview.md)

## <a name="1-connect-to-your-sql-pool"></a>1. Подключитесь к пулу S'L
1. Откройте SSMS.
2. Откройте explorer объектов, выбрав исследователь объектов **файлового** > **подключения.**
   
    ![Обозреватель объектов SQL Server](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. Заполните поля в окне «Подключение к серверу».
   
    ![Подключение к серверу](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Имя сервера**. Введите найденное **имя сервера** .
   * **Проверка подлинности**. Выберите **Проверка подлинности SQL Server** или **Встроенная проверка подлинности Active Directory**.
   * **Имя пользователя** и **пароль**. Если вы выбрали проверку подлинности SQL Server, введите имя пользователя и пароль.
   * Нажмите кнопку **Подключить**.
4. Чтобы исследовать данные, разверните сервер Azure SQL Server. Вы можете просмотреть базы данных, связанные с сервером. Разверните AdventureWorksDW, чтобы просмотреть таблицы в образце базы данных.
   
    ![Обзор AdventureWorksDW](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. Выполнить пример запроса
Теперь, когда мы подключились к базе данных, давайте напишем запрос.

1. Щелкните правой кнопкой мыши базу данных в обозревателе объектов SQL Server.
2. Выберите **Создать запрос**. Откроется новое окно запроса.
   
    ![Создать запрос](./media/sql-data-warehouse-query-ssms/new-query.png)
3. Копируйте следующий запрос T-S'L в окне запроса:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Выполнить запрос, нажав `Execute` или использовать `F5`следующий ярлык: .
   
    ![Выполнение запроса](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. Просмотрите результаты запроса. В этом примере таблица FactInternetSales содержит 60 398 строк.
   
    ![Результаты запроса](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы можете подключиться и заделать запрос, попробуйте [визуализировать данные с Power BI.](sql-data-warehouse-get-started-visualize-with-power-bi.md )
Для настройки среды для проверки подлинности Active Directory, [см.](sql-data-warehouse-authentication.md)
