---
title: Подключение в обозреватель данных Azure с помощью ODBC
description: В этой статье вы узнаете, как настроить подключение к Open Database Connectivity (ODBC) в обозреватель данных Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 02ae9673f1dc402ee1500b466d7e259263ef3262
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66494844"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Подключение в обозреватель данных Azure с помощью ODBC

Open Database Connectivity ([ODBC](/sql/odbc/reference/odbc-overview)) — широко применяемый прикладной программный интерфейс (API) для доступа к базе данных. Использование ODBC для подключения в обозреватель данных Azure из приложений, у которых нет выделенных соединителя.

На самом деле приложения вызывают функции через интерфейс ODBC, которые реализованы в конкретной базе данных модулей, которые называются *драйверы*. Обозреватель данных Azure поддерживает подмножество протокола связи SQL Server ([MS-TDS](/azure/kusto/api/tds/)), поэтому его можно использовать драйвер ODBC для SQL Server.

В этой статье вы узнаете, как использовать драйвер ODBC для SQL Server, чтобы вы могли подключаться к обозреватель данных Azure из любого приложения, поддерживающий ODBC.

## <a name="prerequisites"></a>Технические условия

Кроме этого, вам потребуются:

* [Драйвер Microsoft ODBC для SQL Server версии 17.2.0.1 или более поздней версии](/sql/connect/odbc/download-odbc-driver-for-sql-server) для вашей операционной системы.

## <a name="configure-the-odbc-data-source"></a>Настройка источника данных ODBC

Выполните следующие действия для настройки источника данных ODBC с помощью драйвера ODBC для SQL Server.

1. В Windows, найдите *источников данных ODBC*и откройте классическое приложение источников данных ODBC.

1. Выберите **Добавить**.

    ![Добавление источника данных](media/connect-odbc/add-data-source.png)

1. Выберите **ODBC Driver 17 for SQL Server** затем **Готово**.

    ![Выбор драйвера](media/connect-odbc/select-driver.png)

1. Введите имя и описание для соединения и кластер, вы хотите подключиться, затем выберите **Далее**. URL-адрес должен быть в форме кластера  *\<ClusterName\>.\< Регион\>. kusto.windows.net*.

    ![Выберите сервер](media/connect-odbc/select-server.png)

1. Выберите **интегрированных с Active Directory** затем **Далее**.

    ![Встроенная проверка подлинности Active Directory](media/connect-odbc/active-directory-integrated.png)

1. Затем выберите базу данных с использованием образца данных **Далее**.

    ![Изменение базы данных по умолчанию](media/connect-odbc/change-default-database.png)

1. На следующем экране оставьте все параметры по умолчанию выберите **Готово**.

1. Выберите **проверить источник данных**.

    ![Источник данных теста](media/connect-odbc/test-data-source.png)

1. Убедитесь, что тест успешно выберите **ОК**. Если тест не удается, проверьте значения, заданные на предыдущих шагах и убедитесь, что у вас есть достаточные разрешения для подключения к кластеру.

    ![Проверка завершена успешно](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Подключение к Azure Data Explorer из Tableau](tableau.md)