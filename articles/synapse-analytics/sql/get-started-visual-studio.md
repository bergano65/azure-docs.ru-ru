---
title: Отправка запросов и подключение к Synapse SQL с помощью Visual Studio и SSDT
description: Используйте Visual Studio для отправки запросов к пулу SQL с помощью Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: c8b66e42c5719203eb0f195a5b61e81da8b187e7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658108"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Подключение к Synapse SQL с помощью Visual Studio и SSDT
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Используйте Visual Studio для отправки запросов к пулу SQL с помощью Azure Synapse Analytics. В этом методе используется расширение SQL Server Data Tools (SSDT) в Visual Studio 2019. 

> [!NOTE]
> SQL по запросу (предварительная версия) не поддерживается SSDT.

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим учебником необходимы следующие компоненты.

* Пул SQL. Если у вас его нет, [создайте пул SQL](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Расширение SSDT для Visual Studio. Скорее всего, этот компонент у вас уже есть, если на вашем компьютере установлено приложение Visual Studio. Инструкции по установке и доступные варианты установки приведены в статье [Начало работы с Visual Studio 2019](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Полное имя сервера SQL Server. Чтобы узнать имя сервера, ознакомьтесь с разделом [Подключение к Synapse SQL](connect-overview.md).

## <a name="1-connect-to-sql-pool"></a>1. Подключение к пулу SQL
1. Откройте Visual Studio 2019.
2. Откройте обозреватель объектов SQL Server, выбрав **Вид** > **Обозреватель объектов SQL Server**.
   
    ![Обозреватель объектов SQL Server](./media/get-started-visual-studio/open-ssdt.png)
3. Щелкните значок **Добавить SQL Server** .
   
    ![Добавить SQL Server](./media/get-started-visual-studio/add-server.png)
4. Заполните поля в окне «Подключение к серверу».
   
    ![Подключение к серверу](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Имя сервера**: Введите найденное **имя сервера** .
   * **Аутентификация**. Выберите **Проверка подлинности SQL Server** или **Встроенная проверка подлинности Active Directory**.
   * **Имя пользователя** и **пароль**. Если вы выбрали проверку подлинности SQL Server, введите имя пользователя и пароль.
   * Нажмите кнопку **Соединить**.
5. Чтобы исследовать данные, разверните сервер Azure SQL Server. Вы можете просмотреть базы данных, связанные с сервером. Разверните AdventureWorksDW, чтобы просмотреть таблицы в образце базы данных.
   
    ![Обзор AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Запуск пробного запроса
Теперь, когда вы подключились к базе данных, следует написать запрос.

1. Щелкните правой кнопкой мыши базу данных в обозревателе объектов SQL Server.
2. Выберите **Создать запрос**. Откроется новое окно запроса.
   
    ![Создать запрос](./media/get-started-visual-studio/new-query2.png)
3. Скопируйте следующий запрос T-SQL в окно запроса.
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Запустите запрос, щелкнув зеленую стрелку или нажав клавиши `CTRL`+`SHIFT`+`E`.
   
    ![Выполнение запроса](./media/get-started-visual-studio/run-query.png)
5. Просмотрите результаты запроса. В этом примере таблица FactInternetSales содержит 60 398 строк.
   
    ![Результаты запроса](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы можете подключаться к базе данных и отправлять запросы, попробуйте [визуализировать данные с помощью Power BI](get-started-power-bi-professional.md).
Сведения о том, как настроить аутентификацию Azure Active Directory в своей среде, см. в статье [Аутентификация в Azure синапсе Analytics](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
 