---
title: Использование подключения Open Database Connectivity (ODBC) в обозреватель данных Azure для визуализации данных с Tableau
description: В этой статье вы узнаете, как использовать подключение к Open Database Connectivity (ODBC) для подключения обозревателя данных Azure для визуализации данных с помощью Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: a0948ae35a5c23768df117979db819861ac64529
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499089"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Визуализация данных с помощью обозревателя данных Azure в Tableau

 [Tableau](https://www.tableau.com/) — это платформа визуальной аналитики для бизнес-аналитики. Чтобы подключиться к Azure Data Explorer из Tableau и перенести данные из образца кластера, используйте драйвер SQL Server Open Database Connectivity (ODBC). 

## <a name="prerequisites"></a>Технические условия

Вам потребуется для работы с этой статьей:

* [Подключение в обозреватель данных Azure с помощью ODBC](connect-odbc.md) с помощью драйвера ODBC для SQL Server, для подключения к Azure Data Explorer из Tableau. 

* Tableau Desktop, заполнен, или [пробной](https://www.tableau.com/products/desktop/download) версии.

* Кластер с демонстрационными данными StormEvents. Дополнительные сведения см. в разделе [создание кластера обозреватель данных Azure и базы данных](create-cluster-database-portal.md) и [прием образец данных в обозреватель данных Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Визуализация данных в Tableau 

После завершения настройки ODBC, образец данных можно вернуть в Tableau.

1. В Tableau Desktop в меню слева выберите **другие базы данных (ODBC)** .

    ![Подключение с помощью ODBC](media/tableau/connect-odbc.png)

1. Для **DSN**, выберите источник данных, созданную для ODBC, а затем выберите **Sign In**.

    ![Вход в ODBC](media/tableau/odbc-sign-in.png)

1. Для **базы данных**, например, выберите базу данных в кластере пример *TestDatabase*. Для **схемы**выберите *dbo*, а также для **таблицы**выберите *StormEvents* образец таблицы.

    ![Выберите базу данных и таблицы](media/tableau/select-database-table.png)

1. Tableau теперь показана схема для образца данных. Выберите **обновить** для переноса данных в Tableau.

    ![Обновление данных](media/tableau/update-data.png)

    После импорта данных, Tableau отображаются строки данных на следующем рисунке.

    ![Результирующий набор](media/tableau/result-set.png)

1. Теперь можно создавать визуализации в Tableau, на основе данных, который отображался с помощью обозревателя данных Azure. Дополнительные сведения см. в разделе [Tableau обучения](https://www.tableau.com/learn).

## <a name="next-steps"></a>Дальнейшие действия

* [Написание запросов для обозревателя данных Azure](write-queries.md)