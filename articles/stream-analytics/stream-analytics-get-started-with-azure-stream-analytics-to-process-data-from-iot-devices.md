---
title: Обработка потоков данных IoT в режиме реального времени с помощью аналитики Azure Stream Analytics
description: 'Теги и потоки данных датчиков IoT: обработка данных с использованием Stream Analytics в режиме реального времени'
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 0755131f7d8071e37eadc1339ebc5e122725fa71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426241"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Обработка потоков данных IoT в режиме реального времени с помощью аналитики Azure Stream Analytics

В этой статье вы узнаете, как создать логику обработки потоков для сбора данных с устройств Интернета вещей (IoT). Вы используете реальный случай использования Интернета вещей (IoT), чтобы продемонстрировать, как быстро и экономически создать решение.

## <a name="prerequisites"></a>Предварительные требования

* Создайте бесплатную [подписку На Azure.](https://azure.microsoft.com/pricing/free-trial/)
* Скачать пример запроса и файлы данных с [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot).

## <a name="scenario"></a>Сценарий

Компания Contoso, специализирующаяся в области промышленной автоматизации, полностью автоматизировала свой процесс производства. Оборудование этой компании оснащено датчиками, которые передают потоки данных в режиме реального времени. В этом сценарии руководителю производственного участка необходимо в реальном времени получать аналитические данные с датчиков, чтобы определять шаблоны и предпринимать соответствующие действия. Язык запросов Stream Analytics (SA'L) можно использовать над данными датчиков, чтобы найти интересные закономерности из входящего потока данных.

В этом примере данные генерируются с помощью устройства тегов датчиков Texas Instruments. Полезные данные хранятся в формате JSON и выглядят следующим образом:

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

В реальном сценарии у вас могут быть сотни таких датчиков, создающих события в виде потока. В идеале необходимо устройство шлюза, выполняющее код для отправки этих событий в [Центры событий Azure](https://azure.microsoft.com/services/event-hubs/) или [центры Интернета вещей Azure](https://azure.microsoft.com/services/iot-hub/). Задание Stream Analytics будет принимать эти события из Центров событий и выполнять аналитику в режиме реального времени с помощью запросов. Затем можно отправить результаты на один из [поддерживаемых выходов.](stream-analytics-define-outputs.md)

Для удобства в этом руководстве по началу работы приведен пример файла данных, полученных с реальных устройств SensorTag. На основе этого примера можно выполнять различные запросы и просматривать их результаты. В последующих руководствах вы узнаете, как подключить задание к источникам входных данных и местам назначения выходных данных, а также развернуть его в службе Azure.

## <a name="create-a-stream-analytics-job"></a>Создание задания Stream Analytics

1. На [портале Azure](https://portal.azure.com)выберите **ресурс** из меню левой навигации. Затем выберите **задание Stream Analytics** из **Analytics.**
   
    ![Создание нового задания Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. Введите уникальное имя задания и убедитесь, что отображается правильная подписка. Создайте новую группу ресурсов или выберите существующую из подписки.

1. Выберите место для работы. Используйте одно и то же место для группы ресурсов и всех ресурсов для увеличения скорости обработки и снижения затрат. После создания конфигураций выберите **«Создать».**
   
    ![Создание нового задания Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Создание запроса Azure Stream Analytics
Следующим шагом после создания задания является написание запроса. Вы можете протестировать запросы на образцах данных, не подключив входные данные или вывод к работе.

Скачать [HelloWorldASA-InputStream.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) с GitHub. Затем перейдите на работу в Azure Stream Analytics на портале Azure.

Выберите **запрос** под **топологией вакансий** из левого меню. Затем выберите **ввод образца загрузки.** Загрузите `HelloWorldASA-InputStream.json` файл и выберите **Ok**.

![Плитка "Запрос" на панели мониторинга Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

Обратите внимание, что предварительный просмотр данных автоматически заполняется в таблице **предварительного просмотра ввода.**

![Предварительный просмотр входных данных выборки](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>Запрос: архивация необработанных данных

Запрос к серверу — это самая простая форма запроса, который архивирует все входные данные в место назначения выходных данных. Этот запрос — это запрос по умолчанию, населенный новым заданием Azure Stream Analytics.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

Выберите **запрос теста** и просмотрите результаты в таблице **результатов теста.**

![Результаты теста для запроса Stream Analytics](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Запрос: фильтрация данных по условию

Давайте попробуем отфильтровать результаты на основе состояния. Нам требуется отобразить результаты только тех событий, которые получены из датчика sensorA.

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

Вставьте запрос в редактор и выберите **запрос теста** для просмотра результатов.

![Фильтрация потока данных](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Запрос: оповещение для активации рабочего бизнес-процесса

Теперь давайте детализируем наш запрос. Если требуется отслеживать среднее значение температуры за 30-секундный промежуток времени и отображать результаты только в том случае, если это значение превышает 100 градусов,

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![Запрос с 30-секундным фильтром](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Вы должны увидеть результаты, которые содержат только 245 строк и названия датчиков, где средний умеренный размер превышает 100. В этом запросе поток событий сгруппирован по свойству **dspl**, представляющему собой имя датчика, в **"переворачивающемся" окне** длительностью 30 секунд. Временные запросы должны указать, как вы хотите время для прогресса. Используя положение **TIMESTAMP BY,** вы указали столбец **OUTPUTTIME,** чтобы связать время со всеми временными вычислениями. Для получения подробной информации, прочитайте о [функциях управления временем](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) и [оконных окон](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics).

### <a name="query-detect-absence-of-events"></a>Запрос: обнаружение отсутствия событий

Как написать запрос, чтобы обнаружить отсутствие входящих событий? Давайте найдем последний раз, когда датчик отправлял данные, а затем не отправлял события в течение следующих 5 секунд.

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![Обнаружение отсутствия событий](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Здесь используется **LEFT OUTER JOIN** (левое внешнее соединение) для одного и того же потока данных (самосоединение). При **внутреннем соединении** результат возвращается, только если обнаружено совпадение.  Но если событие с левой стороны соединения не сопоставлено при использовании **левого внешнего соединения**, для всех столбцов справа возвращается строка со значением NULL. Этот метод очень удобно использовать для поиска отсутствия событий. Для получения дополнительной информации [см.](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics)

## <a name="conclusion"></a>Заключение

Цель юга состоит в том, чтобы продемонстрировать, как писать различные запросы Stream Analytics Запрос языка и увидеть результаты в браузере. Тем не менее, это только для того, чтобы вы начали. Обработчик Stream Analytics поддерживает разнообразные входные и выходные данные и может даже использовать функции машинного обучения Azure. Благодаря этому он является надежным средством для анализа потоков данных. Дополнительные сведения о написании запросов см. в статье [Примеры запросов для распространенных шаблонов использования Stream Analytics](stream-analytics-stream-analytics-query-patterns.md).

