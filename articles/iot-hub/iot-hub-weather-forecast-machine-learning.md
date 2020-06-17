---
title: Создание прогноза погоды с помощью Машинного обучения Azure по данным из Центра Интернета вещей
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
ms.openlocfilehash: 5551655843b8d3ed5b6d70f5d6ed3a0eb4d0e92f
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83746974"
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

- Пройдите одно из руководств по [использованию симулятора Raspberry Pi в подключенном режиме](iot-hub-raspberry-pi-web-simulator-get-started.md), например [Подключение онлайн-симулятора Raspberry Pi к Центру Интернета вещей Azure (Node.js)](iot-hub-raspberry-pi-kit-node-get-started.md). В них используется следующее:
  - Активная подписка Azure.
  - Центр Интернета вещей Azure в подписке;
  - клиентское приложение, которое отправляет сообщения в Центр Интернета вещей Azure.
- Учетная запись [Студии машинного обучения Azure (классической)](https://studio.azureml.net/).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Развертывание модели прогнозирования погоды как веб-службы

В этом разделе показано, как получить модель прогнозирования погоды из библиотеки ИИ Azure. Затем вы добавите в модель модуль R-script для очистки данных о температуре и влажности. Наконец, вы развернете модель в качестве прогнозной веб-службы.

### <a name="get-the-weather-prediction-model"></a>Получение модели прогнозирования погоды

В этом разделе показано, как получить модель прогнозирования погоды из Коллекции решений ИИ Azure и открыть ее в Студии машинного обучения Azure (классической).

1. Откройте [страницу модели прогнозирования погоды](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![Открытая страница модели прогнозирования погоды в Коллекции решений ИИ Azure](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Щелкните **Open in Studio (classic)** (Открыть в Studio (классическая)), чтобы открыть модель в Студии машинного обучения Microsoft Azure (классической).

   ![Открытие модели прогнозирования погоды в Студии машинного обучения Azure (классической)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Добавление модуля R-script для очистки данных о температуре и влажности

Для правильной работы модели данные температуры и влажности должны поддерживать преобразование в числовые данные. В этом разделе показано, как добавить в модель прогнозирования погоды модуль R-script, который удаляет все строки, значения температуры и влажности, которые нельзя преобразовать в числовые значения.

1. В левой части окна Студии машинного обучения Azure щелкните стрелку, чтобы развернуть панель инструментов. Введите строку Execute в поле поиска. Выберите модуль **Execute R Script** (Выполнение скрипта R).

   ![Выбор модуля Execute R Script (Выполнение скрипта R)](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Перетащите модуль **Execute R Script** (Выполнение скрипта R) и поместите рядом с модулем **Clean Missing Data** (Очистка отсутствующих данных) и уже существующим на схеме модулем **Execute R Script** (Выполнение скрипта R). Удалите связь между модулями **Clean Missing Data** (Очистка отсутствующих данных) и **Execute R Script** (Выполнение скрипта R), а затем соедините входы и выходы нового модуля, как показано ниже.

   ![Добавление модуля Execute R Script (Выполнение скрипта R)](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Выберите новый модуль **Execute R Script** (Выполнение скрипта R), чтобы открыть окно его свойств. Скопируйте и вставьте следующий код в поле **R Script** (Скрипт R).

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Когда вы завершите работу, окно свойств будет выглядеть примерно так:

   ![Добавление кода в модуль Execute R Script (Выполнение скрипта R)](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Развертывание прогнозной веб-службы

В этом разделе показано, как проверить модель, а также настроить и развернуть прогнозную веб-службу на основе этой модели.

1. Щелкните **Run** (Запуск), чтобы проверить действия модели. Этот шаг может занять несколько минут.

   ![Запуск эксперимента для проверки шагов](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Щелкните действие **SET UP WEB SERVICE** (Настроить веб-службу)  > **Predictive Web Service** (Прогнозная веб-служба). Откроется схема прогнозного эксперимента

   ![Развертывание модели прогнозирования погоды в Студии машинного обучения Azure (классической)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. На схеме прогнозного эксперимента удалите связь между модулями**Web service input** (Входные данные веб-службы) и **Weather Dataset** (Набор данных о погоде) в верхней части страницы. Затем перетащите модуль **Web service input** (Входные данные веб-службы) поближе к модулю **Score Model** (Оценка модели) и подключите его, как показано ниже.

   ![Соединение двух модулей в Студии машинного обучения Azure (классической)](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)

1. Щелкните **RUN** (Запуск), чтобы проверить действия модели.

1. Щелкните **DEPLOY WEB SERVICE** (Развернуть веб-службу), чтобы преобразовать эту модель в веб-службу.

1. На панели мониторинга модели скачайте **Excel 2010 or earlier workbook** (Книга Excel 2010 или более ранних версий) для действия **REQUEST/RESPONSE** (Запрос — ответ).

   > [!Note]
   > Обязательно используйте именно **книгу Excel 2010 или более ранних версий**, даже если на вашем компьютере установлена более поздняя версия Excel.

   ![Скачивание книги Excel для конечной точки REQUEST RESPONSE ("запрос — ответ")](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Откройте эту книгу Excel и запишите параметры **WEB SERVICE URL** (URL веб-службы) и **ACCESS KEY** (Ключ доступа).

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Создание, настройка и выполнение заданий Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Создание задания Stream Analytics

1. На [портале Azure](https://portal.azure.com/) последовательно выберите **Создать ресурс** >  **"Интернет вещей"**  > **Stream Analytics job** (Задание Stream Analytics).
1. Введите представленные ниже сведения для задания.

   **Имя задания.** Имя задания. Оно должно быть глобально уникальным.

   **Группа ресурсов.** Выберите ту же группу ресурсов, которую использует центр Интернета вещей.

   **Расположение.** Выберите то же расположение, которое использует группа ресурсов.

   **Закрепить на панели мониторинга.** Установите этот флажок, чтобы из панели мониторинга быстро получать доступ к центру Интернета вещей.

   ![Создание задания Stream Analytics в Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Нажмите кнопку **Создать**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Добавление входных данных в задание Stream Analytics

1. Откройте задание Stream Analytics.
1. В разделе **Топология задания** щелкните **Входные данные**.
1. В области **Входные данные** щелкните **Добавить**, а затем введите сведения, приведенные ниже.

   **Входной псевдоним.** Уникальный псевдоним для выходных данных.

   **Источник**. Выберите **Центр Интернета вещей**.

   **Группа потребителей.** Выберите созданную группу потребителей.

   ![Добавление входных данных в задание Stream Analytics в Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Нажмите кнопку **Создать**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Добавление выходных данных в задание Stream Analytics

1. В разделе **Топология задания** щелкните **Выходные данные**.
1. В области **Выходные данные** щелкните **Добавить**, а затем введите сведения, приведенные ниже.

   **Выходной псевдоним.** Уникальный псевдоним для выходных данных.

   **Приемник.** Выберите **Хранилище BLOB-объектов**.

   **Учетная запись хранения**. Это учетная запись хранения для вашего хранилища больших двоичных объектов. Можно использовать существующую учетную запись хранения или создать новую.

   **Контейнер.** Это контейнер, в котором хранится большой двоичный объект. Вы можете создать новый контейнер или использовать уже существующий.

   **Формат сериализации событий.** выберите **CSV**.

   ![Добавление выходных данных в задание Stream Analytics в Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Нажмите кнопку **Создать**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Добавление в задание Stream Analytics функции для вызова развернутой веб-службы

1. В разделе **Топология задания** щелкните **Функции** > **Добавить**.
1. Введите следующие сведения:

   **Псевдоним функции.** Введите `machinelearning`.

   **Тип функции.** Выберите **Машинное обучение Azure**.

   **Вариант импорта.** Выберите **Импортировать из другой подписки**.

   **URL-адрес**. Введите URL веб-службы, который вы взяли из книги Excel.

   **Ключ**: Введите значение ACCESS KEY, полученное в книге Excel.

   ![Добавление функции в задание Stream Analytics в Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Нажмите кнопку **Создать**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Настройка запроса задания Stream Analytics

1. В разделе **Топология задания** щелкните **Запрос**.
1. Замените текст запроса следующим кодом.

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[scored probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Замените значение `[YourInputAlias]` значением псевдонима входных данных задания.

   Замените значение `[YourOutputAlias]` значением псевдонима выходных данных задания.

1. Выберите команду **Сохранить**.

### <a name="run-the-stream-analytics-job"></a>Выполнение задания Stream Analytics

В задании Stream Analytics щелкните **Запуск** > **Сейчас** > **Запустить**. После успешного запуска состояние задания **Остановлено** изменится на **Выполняется**.

![Выполнение задания Stream Analytics](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Использование обозревателя службы хранилища Microsoft Azure для просмотра прогноза погоды

Запустите клиентское приложение, чтобы начать сбор и отправку данных о температуре и влажности в Центр Интернета вещей. Для каждого сообщения, полученного Центром Интернета вещей, задание Stream Analytics вызывает веб-службу прогнозирования погоды, чтобы оценить вероятности дождя. Полученный результат сохраняется в хранилище BLOB-объектов Azure. Сохраненные результаты можно просмотреть с помощью обозревателя службы хранилища Azure.

1. [Скачайте и установите обозреватель хранилищ Microsoft Azure](https://storageexplorer.com/).
1. Откройте обозреватель службы хранилища Azure.
1. Войдите в учетную запись Azure.
1. Выберите свою подписку.
1. Щелкните подписку Azure > **Учетные записи хранения** > учетная запись хранения > **Контейнеры больших двоичных объектов** и выберите нужный контейнер.
1. Скачайте CSV-файл, чтобы увидеть результат. Последний столбец содержит прогнозируемую вероятность дождя.

   ![Получение прогноза погоды с помощью машинного обучения Azure](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Сводка

Итак, вы успешно применили машинное обучение Azure, чтобы прогнозировать вероятность дождя на основе данных о температуре и влажности, которые получает Центр Интернета вещей.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
