---
title: Подключение и запрос синапсе SQL с помощью Visual Studio и SSDT
description: Используйте Visual Studio для запроса пула SQL с помощью Azure синапсе Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5e725df5c875f23d82da1fda19bcdf28db548fc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428567"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Подключение к синапсе SQL с помощью Visual Studio и SSDT
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Используйте Visual Studio для запроса пула SQL с помощью Azure синапсе Analytics. Этот метод использует расширение SQL Server Data Tools (SSDT) в Visual Studio 2019. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Поддерживаемые средства для использования SQL по запросу (предварительная версия)

Visual Studio в настоящее время не поддерживается для SQL по запросу (Предварительная версия). Однако Студи данных Azure (Предварительная версия) o — это полностью поддерживаемое средство. SQL Server Management Studio частично поддерживается в версии 18,4 и имеет ограниченные функции, такие как подключение и запросы.

## <a name="prerequisites"></a>Предварительные условия
Для работы с этим руководством необходимо иметь следующие компоненты:

* Существующий пул SQL. Если у вас ее нет, см. раздел [Создание пула SQL](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) для выполнения этого предварительного требования.
* Расширение SSDT для Visual Studio. Если у вас есть Visual Studio, этот компонент, вероятно, уже имеется. Инструкции по установке и доступные варианты установки см. в статье [Установка Visual Studio 2015 и SSDT для хранилища данных SQL](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Полное имя сервера SQL Server. Чтобы найти это, см. раздел [Подключение к пулу SQL](connect-overview.md).

## <a name="1-connect-to-sql-pool"></a>1. подключение к пулу SQL
1. Запустите Visual Studio 2019.
2. Откройте обозреватель объектов SQL Server. Для этого выберите **Просмотреть** > **Обозреватель объектов SQL Server**.
   
    ![Обозреватель объектов SQL Server](./media/get-started-visual-studio/open-ssdt.png)
3. Щелкните значок **Добавить SQL Server** .
   
    ![Добавить SQL Server](./media/get-started-visual-studio/add-server.png)
4. Заполните поля в окне «Подключение к серверу».
   
    ![Подключение к серверу](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Имя сервера**: Введите найденное **имя сервера** .
   * **Проверка**подлинности: выберите **SQL Server аутентификацию** или **Active Directory встроенную проверку подлинности**:
   * **Имя пользователя** и **пароль**. Если вы выбрали проверку подлинности SQL Server, введите имя пользователя и пароль.
   * Нажмите кнопку **Соединить**.
5. Чтобы исследовать данные, разверните сервер Azure SQL Server. Вы можете просмотреть базы данных, связанные с сервером. Разверните AdventureWorksDW, чтобы просмотреть таблицы в образце базы данных.
   
    ![Обзор AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Запуск примера запроса
Теперь, когда установлено соединение с базой данных, необходимо написать запрос.

1. Щелкните правой кнопкой мыши базу данных в обозревателе объектов SQL Server.
2. Выберите **Создать запрос**. Откроется новое окно запроса.
   
    ![Создать запрос](./media/get-started-visual-studio/new-query2.png)
3. Скопируйте следующий запрос T-SQL в окно запроса.
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Выполните запрос. Для этого щелкните зеленую стрелку или воспользуйтесь сочетанием клавиш `CTRL`+`SHIFT`+`E`.
   
    ![Выполнение запроса](./media/get-started-visual-studio/run-query.png)
5. Просмотрите результаты запроса. В этом примере таблица FactInternetSales содержит 60 398 строк.
   
    ![Результаты запроса](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы можете подключаться к базе данных и отправлять запросы, попробуйте [визуализировать данные с помощью Power BI](get-started-power-bi-professional.md).
Сведения о настройке среды для проверки подлинности Azure Active Directory см. в разделе [Аутентификация в пуле SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
 