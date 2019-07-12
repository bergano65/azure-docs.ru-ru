---
title: Визуализация данных из Azure Data Explorer в Power BI с помощью SQL-запроса
description: 'В этой статье вы узнаете, как использовать один из трех параметров для визуализации данных в Power BI: SQL-запрос к кластеру обозреватель данных Azure.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 5378a49ba5a4f3770ec9d9b3f723c063b4ef1a61
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806422"
---
# <a name="visualize-data-from-azure-data-explorer-using-a-sql-query-in-power-bi"></a>Визуализация данных из Azure Data Explorer в Power BI с помощью SQL-запроса

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Power BI — это решение бизнес-аналитики, позволяющее визуализировать данные и делиться результатами с коллегами.

Azure Data Explorer поддерживает три варианта подключения к данным в Power BI: использование встроенного соединителя, импорт запроса из Azure Data Explorer и использование SQL-запроса. В этой статье показано, как использовать SQL-запрос для получения данных и их визуализации в отчете Power BI.

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Вам потребуется для работы с этой статьей:

* учетная запись электронной почты организации, подключенной к Azure Active Directory, для доступа к [кластеру help в Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/samples);

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (выберите **Скачать бесплатно**);

## <a name="get-data-from-azure-data-explorer"></a>Получение данных из Azure Data Explorer

Сначала подключитесь к кластеру help Azure Data Explorer, а затем выведите часть данных из таблицы *StormEvents*. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

Обычно можно использовать собственный язык запросов Azure Data Explorer, но эта служба также поддерживает SQL-запросы, которые мы используем в нашем примере. Azure Data Explorer автоматически преобразует SQL-запрос в собственный запрос.

1. В Power BI Desktop на вкладке **Главная** выберите **Получение данных**, а затем — **Дополнительно**.

    ![Получение данных](media/power-bi-sql-query/get-data-more.png)

1. Введите в поле поиска *База данных SQL Azure*, выберите элемент **База данных SQL Azure** и нажмите кнопку **Подключить**.

    ![Поиск и получение данных](media/power-bi-sql-query/search-get-data.png)

1. В окне **База данных SQL Server** введите в поля формы следующие сведения.

    ![Параметры базы данных, таблицы и запроса](media/power-bi-sql-query/database-table-query.png)

    **Параметр** | **Значение** | **Описание поля**
    |---|---|---|
    | Сервер | *help.kusto.windows.net* | URL-адрес кластера help (без *https://* ). Для других кластеров URL-адрес имеет вид *\<имя_кластера\>.\<регион\>.kusto.windows.net*. |
    | База данных | *Примеры* | Пример базы данных размещен в кластере, к которому вы подключаетесь. |
    | Режим подключения к данным | *Импорт* | Определяет, должно ли решение Power BI импортировать данные или подключаться непосредственно к источнику данных. С этим соединителем можно использовать любой вариант. |
    | Время ожидания команды | Не указывайте | Длительность выполнения запроса до того, как будет выдана ошибка превышения времени ожидания. |
    | Инструкция SQL | Скопируйте запрос под этой таблицей | Инструкция SQL, которую Azure Data Explorer преобразует в собственный запрос. |
    | Другие варианты | Оставьте значения по умолчанию | Параметры, которые не применимы к кластерам Azure Data Explorer. |
    | | | |

    ```SQL
    SELECT TOP 1000 *
    FROM StormEvents
    ORDER BY DamageCrops DESC
    ```

1. Если вы еще не установили подключение к кластеру help, выполните вход. Выполните вход с учетной записью Майкрософт, а затем нажмите кнопку **Подключить**.

    ![Вход](media/power-bi-sql-query/sign-in.png)

1. На экране **help.kusto.windows.net: Samples** (help.kusto.windows.net: Примеры) выберите **Загрузить**.

    ![Загрузка данных](media/power-bi-sql-query/load-data.png)

    Таблица откроется в представлении отчета главного окна Power BI, где можно создавать отчеты на основе примера данных.

## <a name="visualize-data-in-a-report"></a>Визуализация данных в отчете

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если больше не требуется отчет, который вы создали в этой статье, удалите файл Power BI Desktop (pbix).

## <a name="next-steps"></a>Следующие шаги

[Визуализация данных с помощью соединителя обозреватель данных Azure для Power BI](power-bi-connector.md)