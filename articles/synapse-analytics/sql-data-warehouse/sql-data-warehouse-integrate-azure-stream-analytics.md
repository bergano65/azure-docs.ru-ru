---
title: Использование Azure Stream Analytics в выделенном пуле SQL
description: Советы по использованию Azure Stream Analytics с выделенным пулом SQL в Azure синапсе для разработки решений в режиме реального времени.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 8fbe546beb1004214e544f8eb160884c0f64ef9e
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/13/2021
ms.locfileid: "96458221"
---
# <a name="use-azure-stream-analytics-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Использование Azure Stream Analytics с выделенным пулом SQL в Azure синапсе Analytics

Azure Stream Analytics является полностью управляемой службой, которая обеспечивает низкую задержку, высокий уровень доступности и масштабируемую обработку сложных событий посредством потоковой передачи данных в облако. Основные сведения см. в статье [Что такое Stream Analytics?](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Вы также можете узнать о создании комплексного решения с помощью Stream Analytics в руководстве [Приступая к работе с Azure Stream Analytics: выявление мошенничества в режиме реального времени](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

В этой статье вы узнаете, как использовать выделенный пул SQL в качестве приемника вывода для приема данных с высокой пропускной способностью с помощью Azure Stream Analyticsных заданий.

## <a name="prerequisites"></a>Предварительные требования

* Azure Stream Analytics задание. чтобы создать задание Azure Stream Analytics, выполните действия, описанные в руководстве [Приступая к работе с Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .  

    1. Создание ввода концентратора событий
    2. Настройка и запуск приложения генератора событий
    3. Подготовка задания Stream Analytics
    4. Указание входных данных задания и запроса
* Выделенный пул SQL. чтобы создать новый выделенный пул SQL, выполните действия, описанные в [кратком руководстве по созданию выделенного пула SQL](../quickstart-create-sql-pool-portal.md).

## <a name="specify-streaming-output-to-point-to-your-dedicated-sql-pool"></a>Указание потокового вывода для указания на выделенный пул SQL

### <a name="step-1"></a>Шаг 1

В портал Azure перейдите к заданию Stream Analytics и щелкните **выходные данные** в меню **топология задания** .

### <a name="step-2"></a>Шаг 2

Нажмите кнопку **Добавить** и выберите **Azure синапсе Analytics** в раскрывающемся меню.

![Выбор Azure синапсе Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>Шаг 3

Введите следующие значения.

* *Выходной псевдоним*. Введите понятное имя для выходных данных задания.
* *Подписка*:
  * Если выделенный пул SQL находится в той же подписке, что и Stream Analytics задание, щелкните "*" и **выберите Azure синапсе Analytics из подписок**.
  _ Если выделенный пул SQL находится в другой подписке, щелкните "указать параметры Azure синапсе Analytics" вручную.
* *База данных*: Выберите целевую базу данных из раскрывающегося списка.
* *Имя пользователя*: укажите имя пользователя учетной записи, который имеет разрешение на запись в базе данных.
* *Пароль*: укажите пароль для учетной записи указанного пользователя.
* *Таблица*: укажите имя целевой таблицы в базе данных.
* Нажмите кнопку " **сохранить** "

![Заполненная форма Azure синапсе Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>Шаг 4

Прежде чем можно будет выполнить тест, необходимо создать таблицу в выделенном пуле SQL.  Запустите следующий скрипт создания таблицы с помощью SQL Server Management Studio (SSMS) или средства запроса.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>Шаг 5

На портал Azure для задания Stream Analytics щелкните имя задания.  Нажмите кнопку **_Test_* _ в области _*_сведений о выходных данных_*_ .

![Кнопка "тест" на странице Аутпаут сведения о том ](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) , что после завершения подключения к базе данных на портале отображается уведомление.

### <a name="step-6"></a>Шаг 6

Щелкните меню " _*_запрос_*_ " в разделе " _*_топология задания_*_ " и измените запрос, чтобы вставить данные в созданный потоковый выход.  Нажмите кнопку _*_проверить выбранный запрос_*_ , чтобы проверить запрос.  Нажмите кнопку _*_Сохранить запрос_*_ при успешном завершении теста запроса.

![Сохранение запроса](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Шаг 7

Запустите задание Azure Stream Analytics.  Нажмите кнопку _*_Пуск_*_ в меню _*_Обзор_*_ .

![Запуск задания Stream Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Нажмите кнопку _ *_Start_** в области запуск задания.

![Нажмите кнопку Пуск](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Дальнейшие действия

Общие сведения об интеграции см. в разделе [Интеграция других служб](sql-data-warehouse-overview-integrate.md).
Дополнительные советы по разработке см. в разделе [решения по проектированию и приемы программирования для выделенного пула SQL](sql-data-warehouse-overview-develop.md).
