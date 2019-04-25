---
title: Подключение в обозреватель данных Azure с помощью ODBC
description: В этом практическом руководстве вы узнаете, как настроить подключение ODBC в обозреватель данных Azure, а затем использовать это подключение для визуализации данных с помощью Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: d01c825e50e30e3545a0d47e432835c658d677af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60448458"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Подключение в обозреватель данных Azure с помощью ODBC

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) — широко применяемый прикладной программный интерфейс (API) для доступа к базе данных. Использование ODBC для подключения в обозреватель данных Azure из приложений, у которых нет выделенных соединителя.

На самом деле приложения вызывают функции через интерфейс ODBC, которые реализованы в конкретной базе данных модулей, которые называются *драйверы*. Обозреватель данных Azure поддерживает подмножество протокола связи SQL Server ([MS-TDS](/azure/kusto/api/tds/)); таким образом его можно использовать драйвер ODBC для SQL Server.

В этой статье вы узнаете, как использовать драйвер ODBC для SQL Server, чтобы вы могли подключаться к обозреватель данных Azure из любого приложения, поддерживающий ODBC. Можно затем при необходимости подключиться к Azure Data Explorer из Tableau и появляются данные из образца кластера.

## <a name="prerequisites"></a>Технические условия

Для работы с этим практическим руководством необходимо следующее.

* [Драйвер Microsoft ODBC для SQL Server версии 17.2.0.1 или более поздней версии](/sql/connect/odbc/download-odbc-driver-for-sql-server) для вашей операционной системы.

* Если вы хотите выполнить наш пример Tableau, вам также понадобится:

  * Tableau Desktop полный или [пробной](https://www.tableau.com/products/desktop/download) версии.

  * Кластер с демонстрационными данными StormEvents. Дополнительные сведения см. в статьях [Краткое руководство. Создание кластера и базы данных обозревателя данных Azure](create-cluster-database-portal.md) и [Прием демонстрационных данных в обозреватель данных Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-odbc-data-source"></a>Настройка источника данных ODBC

Выполните следующие действия для настройки источника данных ODBC с помощью драйвера ODBC для SQL Server.

1. В Windows, найдите *источников данных ODBC*и откройте классическое приложение источников данных ODBC.

1. Выберите **Добавить**.

    ![Добавление источника данных](media/connect-odbc/add-data-source.png)

1. Выберите **ODBC Driver 17 for SQL Server** затем **Готово**.

    ![Выбор драйвера](media/connect-odbc/select-driver.png)

1. Введите имя и описание для соединения и кластер, вы хотите подключиться, затем выберите **Далее**. URL-адрес должен быть в форме кластера  *\<ClusterName\>.\< Регион\>. kusto.windows.net*.

    ![Выбрать сервер](media/connect-odbc/select-server.png)

1. Выберите **интегрированных с Active Directory** затем **Далее**.

    ![Active Directory — встроенная](media/connect-odbc/active-directory-integrated.png)

1. Затем выберите базу данных с использованием образца данных **Далее**.

    ![Изменение базы данных по умолчанию](media/connect-odbc/change-default-database.png)

1. На следующем экране оставьте все параметры по умолчанию выберите **Готово**.

1. Выберите **проверить источник данных**.

    ![Источник данных теста](media/connect-odbc/test-data-source.png)

1. Убедитесь, что тест успешно выберите **ОК**. Если тест не удается, проверьте значения, заданные на предыдущих шагах и убедитесь, что у вас есть достаточные разрешения для подключения к кластеру.

    ![Проверка завершена успешно](media/connect-odbc/test-succeeded.png)

## <a name="visualize-data-in-tableau-optional"></a>Визуализация данных в Tableau (необязательно)

Теперь после завершения настройки ODBC, можно импортировать демонстрационные данные в Tableau.

1. В Tableau Desktop в меню слева выберите **другие базы данных (ODBC)**.

    ![Подключение с помощью ODBC](media/connect-odbc/connect-odbc.png)

1. Для **DSN**, выберите источник данных, созданную для ODBC, а затем выберите **Sign In**.

    ![Вход в ODBC](media/connect-odbc/odbc-sign-in.png)

1. Для **базы данных**, например, выберите базу данных в кластере пример *TestDatabase*. Для **схемы**выберите *dbo*, а также для **таблицы**выберите *StormEvents* образец таблицы.

    ![Выберите базу данных и таблицы](media/connect-odbc/select-database-table.png)

1. Tableau теперь показана схема для образца данных. Выберите **обновить** для переноса данных в Tableau.

    ![Обновление данных](media/connect-odbc/update-data.png)

    После импорта данных, Tableau отображаются строки данных на следующем рисунке.

    ![Результирующий набор](media/connect-odbc/result-set.png)

1. Теперь можно создавать визуализации в Tableau, на основе данных, который отображался с помощью обозревателя данных Azure. Дополнительные сведения см. в разделе [Tableau обучения](https://www.tableau.com/learn).

## <a name="next-steps"></a>Дальнейшие действия

[Написание запросов для обозревателя данных Azure](write-queries.md)

[Руководство. Визуализация данных из обозревателя данных Azure в Power BI](visualize-power-bi.md)