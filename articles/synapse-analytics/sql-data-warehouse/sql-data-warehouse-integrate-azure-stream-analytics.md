---
title: Используйте аналитику потоков Azure
description: Советы по использованию Azure Stream Analytics с хранилищем данных в Azure Synapse для разработки решений в режиме реального времени.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/5/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: feb7b52c84e5e702202bc668cfda676d291ea82e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350438"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Используйте аналитику потоков Azure с помощью аналитики Azure Synapse

Azure Stream Analytics является полностью управляемой службой, которая обеспечивает низкую задержку, высокий уровень доступности и масштабируемую обработку сложных событий посредством потоковой передачи данных в облако. Основные сведения см. в статье [Что такое Stream Analytics?](../../stream-analytics/stream-analytics-introduction.md) Вы также можете узнать о создании комплексного решения с помощью Stream Analytics в руководстве [Приступая к работе с Azure Stream Analytics: выявление мошенничества в режиме реального времени](../../stream-analytics/stream-analytics-real-time-fraud-detection.md).

В этой статье вы узнаете, как использовать хранилище данных в качестве поглотителя для заданий Azure Stream Analytics.

## <a name="prerequisites"></a>Предварительные требования

* Вакансия аналитики потока Azure - Для создания задания Azure Stream Analytics выполните последующие действия в начале работы с помощью учебника [Azure Stream Analytics:](../../stream-analytics/stream-analytics-real-time-fraud-detection.md)  

    1. Создание ввода концентратора событий
    2. Настройка и запуск приложения генератора событий
    3. Подготовка задания Stream Analytics
    4. Указание входных данных задания и запроса
* Хранилище данных пула Azure Synapse S'L - Для создания нового хранилища данных выполните последующие шаги в [квикстарте для создания нового хранилища данных.](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>Указать выход потоковой передачи, чтобы указать на ваш хранилище данных

### <a name="step-1"></a>Шаг 1

С портала Azure перейдите на работу Stream Analytics и нажмите на **результаты** в меню **топологии вакансий.**

### <a name="step-2"></a>Шаг 2

Нажмите на кнопку **Добавить** и выберите **базу данных S'L** из меню drop down.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutput.png)

### <a name="step-3"></a>Шаг 3.

Введите следующие значения.

* *Выход Alias*: Введите дружественное название для этого вывода задания.
* *Подписка*:
  * Если хранилище данных находится в той же подписке, что и задание Stream Analytics, нажмите на ***базу данных Select S'L из подписки.***
  * Если база данных находится в другой подписке, нажмите на настройки базы данных Provide S'L вручную.
* *База данных*: Выберите базу данных назначения из списка выпадающих.
* *Имя пользователя*: укажите имя пользователя учетной записи, который имеет разрешение на запись в базе данных.
* *Пароль*: укажите пароль для учетной записи указанного пользователя.
* *Таблица*: укажите имя целевой таблицы в базе данных.
* нажмите на кнопку **Сохранить**

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutputdbsettings.png)

### <a name="step-4"></a>Шаг 4.

Прежде чем вы сможете выполнить тест, необходимо создать таблицу в хранилище данных.  Запустите следующий сценарий создания таблицы с помощью s'L Server Management Studio (SSMS) или на выбор инструмента запроса.

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

На портале Azure для работы Stream Analytics нажмите на имя вакансии.  Нажмите на кнопку ***"Тест"*** в панели ***сведений.***

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png)Когда подключение к базе данных будет успешным, вы увидите уведомление на портале.

### <a name="step-6"></a>Шаг 6

Нажмите на меню ***запроса*** в ***топологии вакансий*** и измените запрос, чтобы вставить данные в созданный поток.  Нажмите на выбранную ***кнопку запроса Test,*** чтобы протестировать запрос.  Нажмите кнопку ***«Сохранить запрос»,*** когда тест запроса будет успешным.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Шаг 7

Запустите задание аналитики потоков Azure.  Нажмите на кнопку ***«Пуск»*** в меню ***«Обзор».***

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

нажмите на кнопку ***"Пуск"*** на панели стартового задания.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Дальнейшие действия

Для обзора интеграции [см.](sql-data-warehouse-overview-integrate.md)
Дополнительные советы [ Design decisions and coding techniques for data warehouses](sql-data-warehouse-overview-develop.md)по разработке см.
