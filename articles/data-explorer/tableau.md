---
title: Использование подключения ODBC к Azure обозреватель данных для визуализации данных с помощью Tableau
description: Из этой статьи вы узнаете, как использовать подключение ODBC к Azure обозреватель данных для визуализации данных с помощью Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 4dd8fbd761a3442536919e17bae5465adf6b945f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023864"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Визуализация данных из обозреватель данных Azure в Tableau

 [Tableau](https://www.tableau.com/) — это платформа визуальной аналитики для бизнес-аналитики. Чтобы подключиться к Azure обозреватель данных из Tableau и перенести данные из примера кластера, используйте драйвер SQL Server Open Database Connectivity (ODBC). 

## <a name="prerequisites"></a>предварительным требованиям

Для выполнения этой статьи необходимо следующее:

* [Подключитесь к azure обозреватель данных с помощью ODBC](connect-odbc.md) , используя драйвер ODBC SQL Server, чтобы подключиться к обозреватель данных Azure из Tableau. 

* Tableau Desktop, полная или [пробная](https://www.tableau.com/products/desktop/download) версия.

* Кластер с демонстрационными данными StormEvents. Дополнительные сведения см. в статьях [Создание кластера и базы данных Azure обозреватель данных](create-cluster-database-portal.md) и прием [демонстрационных данных в обозреватель данных Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Визуализация данных в Tableau 

После завершения настройки ODBC можно перенести образцы данных в Tableau.

1. В Tableau Desktop в меню слева выберите **другие базы данных (ODBC)** .

    ![Подключение с помощью ODBC](media/tableau/connect-odbc.png)

1. В поле **DSN**выберите источник данных, СОЗДАННЫЙ для ODBC, а затем щелкните **Вход**.

    ![Вход в ODBC](media/tableau/odbc-sign-in.png)

1. В поле **база данных**выберите базу данных в примере кластера, например *тестдатабасе*. Для **схемы**выберите *dbo*, а для **таблицы**выберите образец таблицы *стормевентс* .

    ![Выбор базы данных и таблицы](media/tableau/select-database-table.png)

1. Tableau теперь показывает схему для демонстрационных данных. Выберите **Обновить сейчас** , чтобы перенести данные в Tableau.

    ![Обновление данных](media/tableau/update-data.png)

    При импорте данных Tableau отображает строки данных, как показано на следующем рисунке.

    ![Результирующий набор](media/tableau/result-set.png)

1. Теперь вы можете создавать визуализации в Tableau на основе данных, которые вы добавили из обозреватель данных Azure. Дополнительные сведения см. в разделе [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Дополнительная информация

* [Написание запросов для обозревателя данных Azure](write-queries.md)