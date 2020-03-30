---
title: Аналитика озер данных запросов - Visual Studio
description: Сведения об установке средств Data Lake для Visual Studio, разработке и тестировании скриптов U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: dacce0d4f40f077b5da6221000192a4398da99e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260349"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Разработка скриптов U-SQL с помощью средств Data Lake для Visual Studio

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Средства Azure Data Lake и Stream Analytics включают функции, близкие к функциям двух служб Azure: Azure Data Lake Analytics и Azure Stream Analytics. Для получения дополнительной информации о сценариях анализа потоков Azure можно ознакомиться на [инструментах Azure Stream Analytics для Visual Studio.](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md)

В этой статье описывается, как использовать Visual Studio для создания учетных записей Azure Data Lake Analytics. Вы можете определить вакансии в [U-S'L](data-lake-analytics-u-sql-get-started.md)и отправить вакансии в службу аналитики Data Lake. Дополнительные сведения о Data Lake Analytics см. в [обзоре Azure Data Lake Analytics](data-lake-analytics-overview.md).

> [!IMPORTANT]
> Мы рекомендуем вам перейти на Azure Data Lake Tools для визуальной студии версии 2.3.3000.4 или позже. Предыдущие версии являются устаревшими и недоступными для скачивания.
>
> 1. Проверьте, не используете ли вы Средства Azure Data Lake для Visual Studio более ранней версии, чем 2.3.3000.4.
>
>    ![Проверка версии средств](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
>
> 1. Если у вас более ранняя версия, чем 2.3.3000.4, обновите Средства Azure Data Lake для Visual Studio, посетив сайт центра загрузки:
>    - [Для визуальной студии 2017 и 2019](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [для Visual Studio 2013 и 2015.](https://www.microsoft.com/en-us/download/details.aspx?id=49504)

## <a name="prerequisites"></a>Предварительные требования

* **Visual Studio**: поддерживаются все выпуски, кроме Express.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK для .NET** (версии 2.7.1 или выше). Можно установить его с помощью [установщика веб-платформы](https://www.microsoft.com/web/downloads/platform.aspx).
* Учетная запись **Data Lake Analytics.** Чтобы создать учетную запись, ознакомьтесь со статьей [Руководство. Начало работы с Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Установка средств Azure Data Lake для Visual Studio

Для работы с этим руководством необходимо установить Data Lake Tools для Visual Studio. Для получения дополнительной информации, [см.](data-lake-analytics-data-lake-tools-install.md)

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Подключение к учетной записи Azure Data Lake Analytics

1. Запустите Visual Studio.

1. Открыть **исследователь серверов,** выбрав **View** > **Server Explorer.**

1. Нажмите справа на **Azure,** а затем выберите **Подключите к подписке Microsoft Azure.** **Войдите в свой аккаунт,** следуйте инструкциям.

1. В **Server Explorer**выберите**аналитику озер данных** **Azure.** >  Отобразится список учетных записей Data Lake Analytics.

## <a name="write-your-first-u-sql-script"></a>Создание первого скрипта U-SQL

Ниже приводится простой скрипт U-SQL. Он определяет небольшой набор данных и по умолчанию записывает его в хранилище Data Lake Store как файл с именем `/data.csv`.

```sql
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>Отправка задания аналитики озера данных

1. В Visual Studio выберите **Файл** > **Новый** > **проект**.

1. Выберите тип **проекта U-S'S,L,** а затем выберите **Следующий**. В **настройке нового проекта**выберите **Создать**.

   Visual Studio создает решение, содержащее файл **Script.usql.**

1. Вставьте сценарий из [Написать свой первый u-S'L сценарий](#write-your-first-u-sql-script) в окне **Script.usql.**

1. В **Solution Explorer**, правой кнопкой мыши **Script.usql**, и выберите **Отправить сценарий**.

1. В **отправке Вакансия**, выберите учетную запись Data Lake Analytics и выберите **Отправить**.

   ![Отправка проекта U-SQL в Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-submit-job-vs2019.png)

После отправки задания откроется вкладка **Представление задания**, на которой отображается ход выполнения задания.

* В окне **Сводные данные задания** представлена сводка задания.
* В окне **Граф задания** визуализируется ход выполнения задания.
* В окне **Операции с метаданными** представлены сведения обо всех действиях, выполненных в каталоге U-SQL.
* В окне **Данные** отображаются все входные и выходные данные.
* **Состояние журнала** отображает сведения о временной шкале и состоянии.
* **Анализ AU** показывает, сколько AUs было использовано в работе и изучить моделирования различных стратегий распределения АС.
* В окне **Диагностика** представлены данные расширенного анализа для выполнения задания и оптимизации производительности.

![График выполнения задания аналитики озера данных U-SQL в Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

Чтобы увидеть последний статус задания и обновить экран, выберите **Refresh.**

## <a name="check-job-status"></a>Проверка состояния задания

1. В **Server Explorer**выберите**аналитику озер данных** **Azure.** > 

1. Разверните окно имени учетной записи Data Lake Analytics.

1. Дважды щелкните **Задания**.

1. Выберите задание, отправленное ранее.

## <a name="see-the-job-output"></a>Просмотр выходных данных задания

1. В **Server Explorer**просмотрите задание, отправленное вами.

1. Перейдите на вкладку **Данные**.

1. На вкладке **Job Outputs** (Выходные данные задания) выберите файл `"/data.csv"`.

## <a name="next-steps"></a>Дальнейшие действия

* [Тестирование и отладка заданий U-SQL с помощью локального выполнения и пакета SDK U-SQL для Azure Data Lake](data-lake-analytics-data-lake-tools-local-run.md)
* [Локальный запуск и локальная отладка U-SQL в Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Использование средств Azure Data Lake для Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
