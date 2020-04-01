---
title: Устранение неполадок з запросами в службе Azure Stream Analytics
description: В этой статье описаны методы устранения неполадок с запросами в заданиях Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: f049dc6d1261a8201cf79d1779e522b30d13c4b0
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409442"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Устранение неполадок з запросами в службе Azure Stream Analytics

В этой статье описаны распространенные проблемы с разработкой запросов Stream Analytics и способы их устранения.

В этой статье описаны общие проблемы с разработкой запросов Azure Stream Analytics, как устранить проблемы запросов и как устранить проблемы. Многие этапы устранения неполадок требуют включения диагностических журналов для работы Stream Analytics. Если нет включенных диагностических журналов, [см.](stream-analytics-job-diagnostic-logs.md)

## <a name="query-is-not-producing-expected-output"></a>Запрос не создает ожидаемых выходных данных

1.  Изучите ошибки с помощью локального тестирования:

    - На портале Azure на вкладке **«Запрос»** выберите **тест.** Для [проверки запроса](stream-analytics-test-query.md) используйте загруженные демонстрационные данные. Проанализируйте все ошибки и попытайтесь исправить их.   
    - Вы также можете [протестировать свой запрос локально,](stream-analytics-live-data-local-testing.md) используя инструменты Azure Stream Analytics для Visual Studio или [Visual Studio Code.](visual-studio-code-local-run-live-input.md) 

2.  [Запросы отогивая насмгия шаг за шагом локально используя диаграмму заданий](debug-locally-using-job-diagram.md) в инструментах Azure Stream Analytics для Visual Studio. Диаграмма задания показывает, как данные перетекают из входных источников (Event Hub, IoT Hub и т.д.) через несколько этапов запроса и, наконец, в выводят раковины. Каждый шаг запроса отображается к временному набору результатов, определенному в скрипте с помощью оператора WITH. Вы можете просматривать данные, а также метрики в каждом промежуточном наборе результатов, чтобы найти источник проблемы.

    ![Результат предварительного просмотра диаграммы задания](./media/debug-locally-using-job-diagram/preview-result.png)

3.  Если используются [**метки времени**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics), убедитесь, что они зарегистрированы для событий после [начала выполнения задания](stream-analytics-out-of-order-and-late-events.md).

4.  Исключите типичные проблемы, например:
    - Предложение [**WHERE**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) в запросе фильтрует все события, предотвращая создание выходных данных.
    - Выполнение функции [**CAST**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) завершается ошибкой, что приводит к сбою всего задания. Чтобы избежать сбоев приведения типов, используйте функцию [**TRY_CAST**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics).
    - При использовании оконных функций подождите до завершения окна для вывода выходных данных из запроса.
    - Отметка времени для событий предшествует времени начала выполнения задания, и события отбрасываются.
    - УСЛОВИЯ [**JOIN**](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) не совпадают. Если совпадений нет, будет нулевой выход.

5.  Убедитесь, что политики упорядочения событий настроены надлежащим образом. Перейдите в **Настройки** и выберите [**заказ событий.**](stream-analytics-out-of-order-and-late-events.md) Эта политика *НЕ* применяется при проверке запроса по нажатию кнопки **Тест**. В результате тестирования в браузере и при выполнении задания в рабочей среде отличаются одним компонентом. 

6. Выполните отладку с помощью журналов аудита и диагностики:
    - Используйте [журналы аудита](../azure-resource-manager/resource-group-audit.md) и фильтр для диагностики и устранения ошибок.
    - Используйте [журналы диагностики заданий](stream-analytics-job-diagnostic-logs.md) для диагностики и устранения ошибок.

## <a name="resource-utilization-is-high"></a>Использование ресурсов является высоким

Воспользуйтесь преимуществами параллелизма в Azure Stream Analytics. Сведения о масштабировании с использованием заданий параллелизации запросов Stream Analytics с помощью настройки входных разделов и определения запроса аналитики см. в статье [Использование параллелизации запросов в Azure Stream Analytics](stream-analytics-parallelization.md).

## <a name="debug-queries-progressively"></a>Последовательная отладка запросов

При обработке данных в режиме реального времени важно знать, как выглядят данные во время запроса. Это можно увидеть с помощью диаграммы задания в Visual Studio. Если у вас нет Visual Studio, вы можете предпринять дополнительные шаги для вывода промежуточных данных.

Так как входные данные или шаги задания Azure Stream Analytics можно считать несколько раз, вы можете написать дополнительные инструкции SELECT INTO. Таким образом вы получаете промежуточные данные в хранилище, на основе которых можно проверить правильность данных, так же, как и с помощью *переменных просмотра* при отладке программы.

В следующем примере запроса в задании Azure Stream Analytics содержится один поток входных данных, два ссылочных набора входных данных и выходные данные для Хранилища таблиц Azure. Запрос объединяет данные из концентратора событий и два ссылочных больших двоичных объекта, чтобы получить сведения об имени и категории:

![Пример запроса SELECT INTO Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Обратите внимание, что задание выполняется, но события не создаются в выходных данных. На плитке **Мониторинг**, показанной здесь, можно увидеть, что данные выводятся, но невозможно определить, на каком шаге операции **JOIN** были удалены все события.

![Плитка мониторинга Azure Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

В этом случае можно добавить несколько дополнительных инструкций SELECT INTO в журнал для регистрации промежуточных результатов операции JOIN и данных, считываемых из входных данных.

В этом примере мы добавили два новых временных набора выходных данных. Они могут представлять собой любой приемник. Ниже в качестве примера используется служба хранилища Azure:

![Добавление дополнительных инструкций SELECT INTO в запрос Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Затем можно переписать запрос следующим образом:

![Перезаписанный запрос SELECT INTO Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Теперь снова запустите задание, которое должно выполняться в течение нескольких минут. Затем запросите temp1 и temp2 с помощью Visual Studio Cloud Explorer для создания следующих таблиц:

**таблица**
![temp1 SELECT INTO temp1 настольный запрос Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**таблица**
![temp2 SELECT INTO temp2 настольный запрос Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Как вы видите, обе таблицы содержат данные, а столбец имени в temp2 заполнен правильно. Тем не менее, так как данные еще не выведены, произошла какая-то проблема:

![Таблица output1 запроса SELECT INTO Stream Analytics без данных](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Выполнив выборку данных, можно почти определенно сказать, что проблема связана со второй операцией JOIN. Вы можете скачать ссылочные данные из большого двоичного объекта и убедиться в этом:

![Запрос SELECT INTO Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Как видно, формат GUID в этих справочных данных отличается от формата столбца в таблице temp2. Именно поэтому данные не поступили в output1, как положено.

Вы можете исправить формат данных, передать их в ссылочный большой двоичный объект и повторить попытку:

![Запрос временной таблицы SELECT INTO Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

На этот раз выходные данные форматируются и заполняются требуемым образом.

![Запрос итоговой таблицы SELECT INTO Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Получить справку

Для получения дополнительной помощи попробуйте наш [форум Azure Stream Analytics.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Следующие шаги

* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Начало использования аналитики потоков Azure](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
