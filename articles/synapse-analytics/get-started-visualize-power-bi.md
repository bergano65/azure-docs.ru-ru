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
ms.openlocfilehash: b72d083216b7cd0ae7a588bfd721d8981829bfc7
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844375"
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
1. Выберите **Создать** > **Подключение к Power BI**.
1. В поле **имя** укажите значение **NYCTaxiWorkspace1**.
1. В поле **Имя рабочей области** укажите **NYCTaxiWorkspace1**.
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



## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Мониторинг](get-started-monitor.md)
                                 

