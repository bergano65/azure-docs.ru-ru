---
title: Прогноз погоды с использованием машинного обучения Azure с данными IoT Hub
description: Используйте машинное обучение Azure, чтобы прогнозировать вероятность дождя на основе данных о температуре и влажности, которые собираются от датчиков в Центре Интернета вещей.
author: robinsh
manager: philmea
keywords: Прогнозирование погоды с помощью машинного обучения
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/10/2020
ms.author: robinsh
ms.openlocfilehash: b71b86c14c55c312ef420a4d8517140fdded4072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122167"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Прогнозирование погоды в машинном обучении Azure с помощью данных от датчиков Центра Интернета вещей

![Комплексная схема](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Машинное обучение — это метод обработки и анализа данных, который позволяет компьютеру на основе исторических данных прогнозировать ожидаемые поведения, результаты и тенденции. Служба машинного обучения Azure — это облачная служба прогнозной аналитики, которая позволяет быстро создавать и развертывать прогнозные модели в качестве решений аналитики.

## <a name="what-you-learn"></a>Что вы узнаете

Вы узнаете, как применить машинное обучение Azure для прогнозирования погоды (вероятности дождя) на основе данных о температуре и влажности, собранных в Центре Интернета вещей. Вероятность дождя вычисляется по заранее подготовленной модели прогнозирования погоды. Эта модель создается на основе исторических данных и применяется для оценки вероятности дождя, исходя из данных о температуре и влажности.

## <a name="what-you-do"></a>Что нужно сделать

- Развернем модель прогнозирования погоды как веб-службу.
- добавим группу потребителей, чтобы обеспечить доступ к данным в Центре Интернета вещей;
- Создадим задание Stream Analytics и настроим его на выполнение следующих задач:
  - получение данных о температуре и влажности от Центра Интернета вещей;
  - вызов веб-службы для получения оценки вероятности дождя;
  - сохранение результатов в хранилище BLOB-объектов Azure;
- Откроем обозреватель хранилища Microsoft Azure для просмотра прогноза погоды.

## <a name="what-you-need"></a>Необходимые элементы

- Завершите [учебник онлайн симулятор Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) или один из учебников по устройству; например, [Raspberry Pi с node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Они охватывают следующие требования:
  - Активная подписка Azure.
  - Центр Интернета вещей Azure в подписке;
  - клиентское приложение, которое отправляет сообщения в Центр Интернета вещей Azure.
- Учетная запись [Студии машинного обучения Azure (классическая).](https://studio.azureml.net/)

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Развертывание модели прогнозирования погоды как веб-службы

В этом разделе вы получите модель прогнозирования погоды из библиотеки Azure AI. Затем вы добавляете модуль R-скрипта к модели для очистки данных о температуре и влажности. Наконец, вы развертываете модель в качестве прогностического веб-сервиса.

### <a name="get-the-weather-prediction-model"></a>Получить модель прогнозирования погоды

В этом разделе вы получите модель прогнозирования погоды из галереи Azure AI и откроете ее в Azure Machine Learning Studio (классический).

1. Откройте [страницу модели прогнозирования погоды](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![Откройте страницу модели прогнозирования погоды в галерее Azure AI](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Нажмите **Открыть в студии (классический),** чтобы открыть модель в Microsoft Azure машинного обучения Studio (классический).

   ![Откройте модель прогнозирования погоды в студии машинного обучения Azure (классический)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Добавьте модуль R-скрипта для очистки данных о температуре и влажности

Для правильного действовав модели данные о температуре и влажности должны быть конвертируемыми в числовые данные. В этом разделе вы добавляете модуль R-скрипта в модель прогнозирования погоды, который удаляет любые строки, которые имеют значения данных для температуры или влажности, которые не могут быть преобразованы в числовые значения.

1. Слева от окна студии машинного обучения Azure щелкните стрелку, чтобы расширить панель инструментов. Введите "Execute" в поле поиска. Выберите модуль **Execute R Script.**

   ![Выберите модуль выполнения R-скрипта](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Перетащите модуль **Execute R Script** рядом с модулем Clean Missing **Data** и существующим модулем Execute **R Script** на диаграмме. Удалите соединение между **чистыми отсутствующими данными** и модулями **Execute R Script,** а затем подключите входные данные и выходы нового модуля в виде показано.

   ![Добавить модуль выполнения R-скрипта](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Выберите новый модуль **Execute R Script,** чтобы открыть окно свойств. Копируйте и вставьте следующий код в поле **R Script.**

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Когда вы закончите, окно свойств должно выглядеть так же, как и следующее:

   ![Добавление кода в модуль «Выполнить R-скриптовый сценарий»](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Развертывание прогностического веб-сервиса

В этом разделе вы проверяете модель, настраиваете прогностический веб-сервис на основе модели, а затем развертываете веб-службу.

1. Нажмите **Выполнить** для проверки шагов в модели. Этот шаг может занять несколько минут.

   ![Выполнить эксперимент для проверки шагов](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Нажмите **SET UP WEB SERVICE** > **Прогностический веб-сервис**. Открывается диаграмма прогностического эксперимента.

   ![Развертывание модели прогнозирования погоды в студии машинного обучения Azure (классический)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. На диаграмме прогностического эксперимента удалите соединение между модулем **ввода Web-сервиса** и **набором данных погоды** в верхней части. Затем перетащите модуль **ввода Web-сервиса** где-нибудь рядом с модулем **Score Model** и подключите его под показано:

   ![Подключение двух модулей в студии машинного обучения Azure (классический)](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)

1. Щелкните **RUN** (Запуск), чтобы проверить действия модели.

1. Щелкните **DEPLOY WEB SERVICE** (Развернуть веб-службу), чтобы преобразовать эту модель в веб-службу.

1. На панели мониторинга модели скачайте **Excel 2010 or earlier workbook** (Книга Excel 2010 или более ранних версий) для действия **REQUEST/RESPONSE** (Запрос — ответ).

   > [!Note]
   > Убедитесь, что вы загружаете **Excel 2010 или более раннюю трудовую книжку,** даже если вы работаете более позднюю версию Excel на вашем компьютере.

   ![Скачивание книги Excel для конечной точки REQUEST RESPONSE ("запрос — ответ")](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Откройте эту книгу Excel и запишите параметры **WEB SERVICE URL** (URL веб-службы) и **ACCESS KEY** (Ключ доступа).

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Создание, настройка и выполнение заданий Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Создание задания Stream Analytics

1. На [портале Azure](https://portal.azure.com/)нажмите **«Создайте работу по поиску ресурсов** > **Internet of Things** > **Stream Analytics».**
1. Введите представленные ниже сведения для задания.

   **Имя работы**: Название задания. Оно должно быть глобально уникальным.

   **Группа ресурсов**: Используйте ту же группу ресурсов, что и концентратор IoT.

   **Расположение**. Выберите то же расположение, которое использует группа ресурсов.

   **Закрепить на панели мониторинга**. Установите этот флажок, чтобы быстро открывать Центр Интернета вещей с помощью панели мониторинга.

   ![Создание задания Stream Analytics в Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Нажмите кнопку **Создать**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Добавление входных данных в задание Stream Analytics

1. Откройте задание Stream Analytics.
1. В разделе **Топология задания** щелкните **Входные данные**.
1. В области **Входные данные** щелкните **Добавить**, а затем введите сведения, приведенные ниже.

   **Входной псевдоним**. Уникальный псевдоним для входных данных.

   **Источник**. Выберите **Центр Интернета вещей**.

   **Группа потребителей.** Выберите созданную группу потребителей.

   ![Добавление входных данных в задание Stream Analytics в Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Нажмите кнопку **Создать**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Добавление выходных данных в задание Stream Analytics

1. В разделе **Топология задания** щелкните **Выходные данные**.
1. В области **Выходные данные** щелкните **Добавить**, а затем введите сведения, приведенные ниже.

   **Выходной псевдоним**. Уникальный псевдоним для выходных данных.

   **Раковина**: Выберите **Blob хранения**.

   **Учетная запись хранения.** Это учетная запись хранения для вашего BLOB-объекта. Можно использовать существующую учетную запись хранения или создать новую.

   **Контейнер.** Это контейнер, в котором сохранен большой двоичный объект. Вы можете создать новый контейнер или использовать уже существующий.

   **Формат сериализации событий.** Выберите вариант **CSV**.

   ![Добавление выходных данных в задание Stream Analytics в Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Нажмите кнопку **Создать**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Добавление в задание Stream Analytics функции для вызова развернутой веб-службы

1. В разделе **Топология задания** щелкните **Функции** > **Добавить**.
1. Введите следующие сведения:

   **Псевдоним функции**: введите значение `machinelearning`.

   **Тип функции**: Выберите **Azure ML**.

   **Метод импорта**: выберите **Импортировать из другой подписки**.

   **URL-адрес**: введите URL веб-службы, который вы взяли из книги Excel.

   **Ключ**: введите ключ доступа, который вы взяли из книги Excel.

   ![Добавление функции в задание Stream Analytics в Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Нажмите кнопку **Создать**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Настройка запроса задания Stream Analytics

1. В разделе **Топология задания** щелкните **Запрос**.
1. Замените текст запроса следующим кодом.

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Замените значение `[YourInputAlias]` значением псевдонима входных данных задания.

   Замените значение `[YourOutputAlias]` значением псевдонима выходных данных задания.

1. Нажмите **Сохранить**.

### <a name="run-the-stream-analytics-job"></a>Выполнение задания Stream Analytics

В заданиях Stream Analytics нажмите **Start** > **Now** > **Start.** После успешного запуска состояние задания **Остановлено** изменится на **Выполняется**.

![Выполнение задания Stream Analytics](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Использование обозревателя службы хранилища Microsoft Azure для просмотра прогноза погоды

Запустите клиентское приложение, чтобы начать сбор и отправку данных о температуре и влажности в Центр Интернета вещей. Для каждого сообщения, полученного Центром Интернета вещей, задание Stream Analytics вызывает веб-службу прогнозирования погоды, чтобы оценить вероятности дождя. Полученный результат сохраняется в хранилище BLOB-объектов Azure. Сохраненные результаты можно просмотреть с помощью обозревателя службы хранилища Azure.

1. [Скачайте и установите обозреватель хранилищ Microsoft Azure](https://storageexplorer.com/).
1. Откройте обозреватель службы хранилища Azure.
1. Войдите в учетную запись Azure.
1. Выберите свою подписку.
1. Щелкните подписку Azure > **Учетные записи хранения** > учетная запись хранения > **Контейнеры больших двоичных объектов** и выберите нужный контейнер.
1. Загрузите файл .csv, чтобы увидеть результат. Последний столбец содержит прогнозируемую вероятность дождя.

   ![Получение прогноза погоды с помощью машинного обучения Azure](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Сводка

Итак, вы успешно применили машинное обучение Azure, чтобы прогнозировать вероятность дождя на основе данных о температуре и влажности, которые получает Центр Интернета вещей.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]