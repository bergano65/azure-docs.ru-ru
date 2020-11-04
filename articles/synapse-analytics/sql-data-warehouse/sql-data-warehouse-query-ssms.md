---
title: Подключение к SSMS
description: Используйте SQL Server Management Studio (SSMS) для подключения к выделенному пулу SQL и выполнения запросов к нему в Azure синапсе Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c2fab8f02f31897a2e3d38094b9bd365f8e921e9
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325323"
---
# <a name="connect-to-a-dedicated-sql-pool-in-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>Подключение к выделенному пулу SQL в Azure синапсе Analytics с помощью SQL Server Management Studio (SSMS)

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Машинное обучение Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Используйте SQL Server Management Studio (SSMS) для подключения к хранилищу данных в Azure синапсе и выполнения запросов к нему.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством необходимы указанные ниже компоненты.

* Существующий выделенный пул SQL. Чтобы создать его, см. раздел [Создание выделенного пула SQL](create-data-warehouse-portal.md).
* Установленный SQL Server Management Studio (SSMS). [Скачайте SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) бесплатно, если у вас его еще нет.
* Полное имя сервера SQL Server. Чтобы найти эти сведения, см. раздел [Подключение к выделенному пулу SQL](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-dedicated-sql-pool"></a>1. подключение к выделенному пулу SQL

1. Откройте SSMS.
2. Откройте обозреватель объектов, выбрав **файл**  >  **подключить обозреватель объектов**.

    ![Обозреватель объектов SQL Server](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. Заполните поля в окне «Подключение к серверу».

   ![Подключение к серверу](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)

   * **Имя сервера**. Введите найденное **имя сервера** .
   * **Проверка подлинности**. Выберите **Проверка подлинности SQL Server** или **Встроенная проверка подлинности Active Directory**.
   * **Имя пользователя** и **пароль**. Если вы выбрали проверку подлинности SQL Server, введите имя пользователя и пароль.
   * Нажмите кнопку **Соединить**.
4. Чтобы исследовать данные, разверните сервер Azure SQL Server. Вы можете просмотреть базы данных, связанные с сервером. Разверните AdventureWorksDW, чтобы просмотреть таблицы в образце базы данных.

   ![Обзор AdventureWorksDW](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. Запуск пробного запроса

Теперь, когда мы подключились к базе данных, давайте напишем запрос.

1. Щелкните правой кнопкой мыши базу данных в обозревателе объектов SQL Server.
2. Выберите **Создать запрос**. Откроется новое окно запроса.

   ![Создать запрос](./media/sql-data-warehouse-query-ssms/new-query.png)
3. Скопируйте следующий запрос T-SQL в окно запроса.

   ```sql
   SELECT COUNT(*) FROM dbo.FactInternetSales;
   ```

4. Выполните запрос, щелкнув `Execute` или воспользовавшись следующим сочетанием клавиш: `F5` .

   ![Выполнение запроса](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. Просмотрите результаты запроса. В этом примере таблица FactInternetSales содержит 60 398 строк.

   ![Результаты запроса](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы можете подключаться к базе данных и отправлять запросы, попробуйте [визуализировать данные с помощью Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md). Сведения о настройке среды для проверки подлинности Azure Active Directory см. [в разделе аутентификация в выделенном пуле SQL](sql-data-warehouse-authentication.md).
