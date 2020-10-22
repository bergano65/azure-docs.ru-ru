---
title: Руководство по началу работы с Azure Synapse Analytics. Визуализация данных рабочей области с помощью Power BI
description: В этом учебнике вы узнаете, как создать рабочую область Power BI, привязать к ней рабочую область Azure Synapse и создать набор данных Power BI, использующий данные из рабочей области Azure Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: business-intelligence
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: c5c41b98d52a4b8182fb162c3bfa9a08c0df5e15
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92171850"
---
# <a name="visualize-data-with-power-bi"></a>Визуализация данных с помощью Power BI

В этом учебнике вы узнаете, как создать рабочую область Power BI, привязать к ней рабочую область Azure Synapse и создать набор данных Power BI, использующий данные из вашей рабочей области Azure Synapse. 

## <a name="overview"></a>Обзор

Из данных нью-йоркского такси мы создали агрегированные наборы данных в двух таблицах:
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

Рабочую область Power BI можно связать с рабочей областью Azure Synapse. Это позволяет легко передавать данные в рабочую область Power BI. Вы можете изменить отчеты Power BI непосредственно в рабочей области Azure Synapse.

### <a name="create-a-power-bi-workspace"></a>Создание рабочей области Power BI

1. Войдите на портал [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Создайте новую рабочую область Power BI с именем **NYCTaxiWorkspace1**.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Связывание рабочей области Azure Synapse с новой рабочей областью Power BI

1. В Synapse Studio последовательно выберите команду **Управление** > **Cвязанные службы**.
1. Выберите команду **Создать** > **Подключиться к Power BI**, и задайте следующие поля:

    |Параметр | Рекомендуемое значение | 
    |---|---|
    |**имя**;|**NYCTaxiWorkspace1**|
    |**Имя рабочей области**|**NYCTaxiWorkspace1**|

1. Нажмите кнопку **создания**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Создание набора данных Power BI, использующего данные в рабочей области Azure Synapse

1. В Synapse Studio перейдите в **Разработка** > **Power BI**.
1. Перейдите в раздел **NYCTaxiWorkspace1** > **Наборы данных Power BI** и выберите **Новый набор данных Power BI**.
1. Наведите указатель мыши на базу данных **SQLDB1** и выберите **Download .pbids file** (Загрузить файл .pbids).
1. Откройте загруженный файл **.pbids**. Рабочий стол Power BI открывает и автоматически подключается к **SQLDB1** в рабочей области Azure Synapse.
1. Если откроется диалоговое окно с именем **База данных SQL Server**:
    1. Выберите **Учетная запись Майкрософт**.
    1. Выберите **Войти** и войдите в свою учетную запись.
    1. Выберите **Подключиться**.
1. После того как откроется диалоговое окно **Навигатор**, проверьте таблицу **PassengerCountStats** и выберите команду **Загрузить**.
1. После того как откроется диалоговое окно **Параметры подключения**, выберите команду **DirectQuery** > **ОК**.
1. Нажмите кнопку **Отчет** слева.
1. Добавьте в отчет **График**.
    1. Перетащите столбец **PassengerCount** в **Визуализации** > **Ось**.
    1. Перетащите столбцы **SumTripDistance** и **AvgTripDistance** в **Визуализации** > **Значения**.
1. На вкладке **Главная** нажмите кнопку **Опубликовать**.
1. Выберите **Сохранить**, чтобы сохранить изменения.
1. Выберите имя файла **PassengerAnalysis.pbix**, а затем выберите команду **Сохранить**.
1. В окне **Выбор назначения** выберите **NYCTaxiWorkspace1**, а затем щелкните **Выбрать**.
1. Дождитесь завершения публикации.

### <a name="configure-authentication-for-your-dataset"></a>Настройка проверки подлинности для набора данных

1. Откройте [powerbi.microsoft.com](https://powerbi.microsoft.com/) и выполните **Вход**.
1. В левой части в разделе **Рабочие области** выберите рабочую область **NYCTaxiWorkspace1**.
1. В этой рабочей области выберите набор данных с именем **Анализ пассажиров** и отчет с именем **Анализ пассажиров**.
1. Наведите указатель мыши на набор данных **PassengerAnalysis**, нажмите кнопку с многоточием (...), а затем выберите команду **Параметры**.
1. В разделе **Учетные данные источника данных** задайте для параметра **Метод аутентификации** значение **OAuth2** и выберите **Вход**.

### <a name="edit-a-report-in-synapse-studio"></a>Изменение отчета в Synapse Studio

1. Вернитесь в Synapse Studio и выберите **Закрыть и обновить**.
1. Перейдите в центр **Разработка**.
1. Наведите указатель мыши на **Power BI** и выберите обновление узла **Отчеты Power BI**.
1. Теперь в **Power BI** должны отобразиться:
    * В разделе **NYCTaxiWorkspace1** > **Наборы данных Power BI**, новый набор с именем **PassengerAnalysis**.
    * В разделе **NYCTaxiWorkspace1** > **Отчеты Power BI**, новый отчет с именем **PassengerAnalysis**.
1. Выберите отчет **PassengerAnalysis**. Отчет откроется, и его можно изменить непосредственно в Synapse Studio.

## <a name="monitor-activities"></a>Действия Monitor

1. В Synapse Studio перейдите в центр **монитор**.
1. Здесь вы можете просмотреть журнал всех действий, выполняемых в рабочей области, и какие из них сейчас активны.
1. Изучите разделы **Выполнения конвейеров**, **Приложения Apache Spark** и **Запросы SQL**, а также просмотрите, что вы уже сделали в рабочей области.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Мониторинг](get-started-monitor.md)
                                 

