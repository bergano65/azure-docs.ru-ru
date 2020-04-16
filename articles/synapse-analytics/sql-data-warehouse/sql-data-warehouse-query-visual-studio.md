---
title: Связь с VSTS
description: Аналитика запросов Azure Synapse с Visual Studio.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 174ee07e389e598fed6ed8487e60303fbce81f77
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416046"
---
# <a name="connect-to-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Подключение к аналитике Azure Synapse с помощью Visual Studio и SSDT
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Машинное обучение Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Используйте Visual Studio для запроса пула S'L в Azure Synapse всего за несколько минут. Этот метод использует расширение s'L Server Data Tools (SSDT) в Visual Studio 2019. 

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим руководством необходимы указанные ниже компоненты.

* Существующий пул S'L. Чтобы создать его, [см.](create-data-warehouse-portal.md)
* Расширение SSDT для Visual Studio. Если у вас есть Visual Studio, вы, вероятно, уже SSDT для визуальной студии. Инструкции по установке и доступные варианты установки см. в статье [Установка Visual Studio 2015 и SSDT для хранилища данных SQL](sql-data-warehouse-install-visual-studio.md).
* Полное имя сервера SQL Server. Чтобы найти эту информацию, [перейдите на номер Connect to s-L.](../sql/connect-overview.md)

## <a name="1-connect-to-your-sql-pool"></a>1. Подключитесь к пулу S'L
1. Откройте Visual Studio 2019.
2. Откройте для себя исследователь объектов сервера, выбрав **view** > **S'L Server Object Explorer.**
   
    ![Обозреватель объектов SQL Server](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Щелкните значок **Добавить SQL Server** .
   
    ![Добавить SQL Server](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Заполните поля в окне «Подключение к серверу».
   
    ![Подключение к серверу](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Имя сервера**. Введите найденное **имя сервера** .
   * **Проверка подлинности**. Выберите **Проверка подлинности SQL Server** или **Встроенная проверка подлинности Active Directory**.
   * **Имя пользователя** и **пароль**. Если вы выбрали проверку подлинности SQL Server, введите имя пользователя и пароль.
   * Нажмите кнопку **Подключить**.
5. Чтобы исследовать данные, разверните сервер Azure SQL Server. Вы можете просмотреть базы данных, связанные с сервером. Разверните AdventureWorksDW, чтобы просмотреть таблицы в образце базы данных.
   
    ![Обзор AdventureWorksDW](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Выполнить пример запроса
Теперь, когда мы подключились к базе данных, давайте напишем запрос.

1. Щелкните правой кнопкой мыши базу данных в обозревателе объектов SQL Server.
2. Выберите **Создать запрос**. Откроется новое окно запроса.
   
    ![Создать запрос](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Копируйте следующий запрос T-S'L в окне запроса:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Выполнить запрос, нажав на зеленую стрелку `CTRL` + `SHIFT` +или использовать следующий ярлык: `E`.
   
    ![Выполнение запроса](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Просмотрите результаты запроса. В этом примере таблица FactInternetSales содержит 60 398 строк.
   
    ![Результаты запроса](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы можете подключиться и заделать запрос, попробуйте [визуализировать данные с Power BI.](sql-data-warehouse-get-started-visualize-with-power-bi.md)

Для настройки среды для проверки подлинности Active Directory, [см.](sql-data-warehouse-authentication.md)
