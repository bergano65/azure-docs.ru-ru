---
title: Подключение к Azure обозреватель данных с помощью ODBC
description: Из этой статьи вы узнаете, как настроить подключение по протоколу ODBC к Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034028"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Подключение к Azure обозреватель данных с помощью ODBC

[ODBC](/sql/odbc/reference/odbc-overview)— это широко принятый интерфейс прикладного программирования (API) для доступа к базе данных. Используйте ODBC для подключения к Azure обозреватель данных из приложений, у которых нет выделенного соединителя.

В фоновом режиме приложения вызывают функции в интерфейсе ODBC, которые реализуются в модулях, связанных с базами данных, которые называются *драйверами*. Azure обозреватель данных поддерживает подмножество протокола связи SQL Server ([MS-TDS](/azure/kusto/api/tds/)), поэтому он может использовать драйвер ODBC для SQL Server.

С помощью следующего видео вы можете научиться создавать подключение ODBC. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Кроме того, можно [настроить источник данных ODBC](#configure-the-odbc-data-source) , как описано ниже. 

В этой статье вы узнаете, как использовать драйвер ODBC SQL Server, чтобы вы могли подключиться к обозреватель данных Azure из любого приложения, которое поддерживает ODBC. 

## <a name="prerequisites"></a>предварительным требованиям

Кроме этого, вам потребуются:

* [Microsoft ODBC Driver for SQL Server версии 17.2.0.1 или более поздней](/sql/connect/odbc/download-odbc-driver-for-sql-server) для операционной системы.

## <a name="configure-the-odbc-data-source"></a>Настройка источника данных ODBC

Выполните следующие действия, чтобы настроить источник данных ODBC с помощью драйвера ODBC для SQL Server.

1. В Windows найдите *Источники данных ODBC*и откройте классическое приложение источники данных ODBC.

1. Выберите **Добавить**.

    ![Добавление источника данных](media/connect-odbc/add-data-source.png)

1. Выберите **ODBC Driver 17 для SQL Server** затем нажмите кнопку **Готово**.

    ![Выбор драйвера](media/connect-odbc/select-driver.png)

1. Введите имя и описание подключения, а также кластер, к которому нужно подключиться, а затем нажмите кнопку **Далее**. URL-адрес кластера должен быть в формате *\<имя_кластера\>.\<Region\>. kusto.Windows.NET*.

    ![Выбор сервера](media/connect-odbc/select-server.png)

1. Выберите **Active Directory интеграция** и **Далее**.

    ![Встроенная Active Directory](media/connect-odbc/active-directory-integrated.png)

1. Выберите базу данных с образцом данных **Далее**.

    ![Изменить базу данных по умолчанию](media/connect-odbc/change-default-database.png)

1. На следующем экране оставьте все параметры по умолчанию, а затем нажмите кнопку **Готово**.

1. Выберите **проверить источник данных**.

    ![Проверить источник данных](media/connect-odbc/test-data-source.png)

1. Убедитесь, что тест успешно прошел, и нажмите кнопку **ОК**. Если тест не был выполнен успешно, проверьте значения, указанные на предыдущих шагах, и убедитесь, что у вас есть необходимые разрешения для подключения к кластеру.

    ![Проверка прошла](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Дополнительная информация

* [Подключение к обозреватель данных Azure из Tableau](tableau.md)