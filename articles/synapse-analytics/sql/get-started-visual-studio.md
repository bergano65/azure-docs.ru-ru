---
title: Подключение к запросу и запросу Synapse S'L с помощью Визуальной студии и SSDT
description: Используйте Visual Studio для запроса пула S'L с помощью Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5e725df5c875f23d82da1fda19bcdf28db548fc8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428567"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Подключитесь к Synapse S'L с помощью визуальной студии и SSDT
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [Sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Используйте Visual Studio для запроса пула S'L с помощью Azure Synapse Analytics. Этот метод использует расширение s'L Server Data Tools (SSDT) в Visual Studio 2019. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Поддерживаемые инструменты для S'L по требованию (предварительный просмотр)

Визуальная студия в настоящее время не поддерживается для S'L по требованию (предварительный просмотр). Тем не менее, Azure Data Studi (предварительный просмотр) o является полностью поддерживаемым инструментом. Студия управления серверами S'L частично поддерживается из версии 18.4 и имеет ограниченные функции, такие как подключение и запрос.

## <a name="prerequisites"></a>Предварительные требования
Чтобы использовать этот учебник, вы должны иметь следующие компоненты:

* Существующий пул S'L. Если у вас его нет, [см.](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* Расширение SSDT для Visual Studio. Если у вас есть Visual Studio, вы, вероятно, уже есть этот компонент. Инструкции по установке и доступные варианты установки см. в статье [Установка Visual Studio 2015 и SSDT для хранилища данных SQL](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Полное имя сервера SQL Server. Чтобы найти это, [см.](connect-overview.md)

## <a name="1-connect-to-sql-pool"></a>1. Подключитесь к пулу S'L
1. Откройте Visual Studio 2019.
2. Откройте исследователь объектов сервера S'L. Для этого выберите **View** > **S'L Server Object Explorer.**
   
    ![Обозреватель объектов SQL Server](./media/get-started-visual-studio/open-ssdt.png)
3. Щелкните значок **Добавить SQL Server** .
   
    ![Добавить SQL Server](./media/get-started-visual-studio/add-server.png)
4. Заполните поля в окне «Подключение к серверу».
   
    ![Подключение к серверу](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Имя сервера**: Введите **имя сервера,** которое было определено ранее.
   * **Аутентификация**: Выберите **проверку подлинности сервера S'L** или **активную интегрированную аутентификацию каталогов:**
   * **Имя пользователя** и **пароль**: Введите имя пользователя и пароль, если выше была выбрана проверка подлинности сервера S'L.
   * Нажмите кнопку **Подключить**.
5. Чтобы исследовать данные, разверните сервер Azure SQL Server. Вы можете просмотреть базы данных, связанные с сервером. Разверните AdventureWorksDW, чтобы просмотреть таблицы в образце базы данных.
   
    ![Обзор AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Выполнить пример запроса
Теперь, когда подключение было установлено в базе данных, вы напишете запрос.

1. Щелкните правой кнопкой мыши базу данных в обозревателе объектов SQL Server.
2. Выберите **Создать запрос**. Откроется новое окно запроса.
   
    ![Создать запрос](./media/get-started-visual-studio/new-query2.png)
3. Копируйте следующий запрос T-S'L в окне запроса:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Выполните запрос. Для этого щелкните зеленую стрелку или воспользуйтесь сочетанием клавиш `CTRL`+`SHIFT`+`E`.
   
    ![Выполнение запроса](./media/get-started-visual-studio/run-query.png)
5. Просмотрите результаты запроса. В этом примере таблица FactInternetSales содержит 60 398 строк.
   
    ![Результаты запроса](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы можете подключиться и заделать запрос, попробуйте [визуализировать данные с Power BI.](get-started-power-bi-professional.md)
Для настройки среды для проверки подлинности Active Directory, [см.](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
 