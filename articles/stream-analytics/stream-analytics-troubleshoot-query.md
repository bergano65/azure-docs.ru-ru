---
title: Устранение неполадок з запросами в службе Azure Stream Analytics
description: В этой статье описаны методы устранения неполадок с запросами в заданиях Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: bf0740bbdd4754aeba43e64f1076a1bea33cffc6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844431"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Устранение неполадок з запросами в службе Azure Stream Analytics

В этой статье описаны распространенные проблемы с разработкой запросов Stream Analytics и способы их устранения.

## <a name="query-is-not-producing-expected-output"></a>Запрос не создает ожидаемых выходных данных
1.  Изучите ошибки с помощью локального тестирования:
    - На портал Azure на вкладке **запрос** выберите **тест**. Для [проверки запроса](stream-analytics-test-query.md) используйте загруженные демонстрационные данные. Проанализируйте все ошибки и попытайтесь исправить их.   
    - Вы также можете [проверить запрос локально](stream-analytics-live-data-local-testing.md) с помощью средств Azure Stream Analytics для Visual Studio или [Visual Studio Code](visual-studio-code-local-run-live-input.md). 

2.  Пошаговая [Отладка запросов с помощью схемы заданий](debug-locally-using-job-diagram.md) в средствах Azure Stream Analytics для Visual Studio. Схема задания показывает, как потоки данных из источников ввода (концентратор событий, центр Интернета вещей и т. д.) выполняются с помощью нескольких шагов запроса и, наконец, выводятся в приемники. Каждый шаг запроса сопоставляется с временным результирующим набором, определенным в скрипте с помощью инструкции WITH. Вы можете просмотреть данные и метрики на каждом шаге запроса в каждом промежуточном результирующем наборе, чтобы найти источник проблемы.
    ](./media/debug-locally-using-job-diagram/preview-result.png) результатов предварительной версии диаграммы заданий ![

3.  Если используются [**метки времени**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics), убедитесь, что они зарегистрированы для событий после [начала выполнения задания](stream-analytics-out-of-order-and-late-events.md).

4.  Исключите типичные проблемы, например:
    - Предложение [**WHERE**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) в запросе фильтрует все события, предотвращая создание выходных данных.
    - Выполнение функции [**CAST**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) завершается ошибкой, что приводит к сбою всего задания. Чтобы избежать сбоев приведения типов, используйте функцию [**TRY_CAST**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics).
    - При использовании оконных функций подождите до завершения окна для вывода выходных данных из запроса.
    - Метка времени для событий предшествует времени запуска задания, поэтому события сбрасываются.

5.  Убедитесь, что политики упорядочения событий настроены надлежащим образом. Перейдите к **параметрам** и выберите [**Упорядочение событий**](stream-analytics-out-of-order-and-late-events.md). Эта политика *НЕ* применяется при проверке запроса по нажатию кнопки **Тест**. В результате тестирования в браузере и при выполнении задания в рабочей среде отличаются одним компонентом. 

6. Выполните отладку с помощью журналов аудита и диагностики:
    - Используйте [журналы аудита](../azure-resource-manager/resource-group-audit.md) и фильтр для диагностики и устранения ошибок.
    - Используйте [журналы диагностики заданий](stream-analytics-job-diagnostic-logs.md) для диагностики и устранения ошибок.

## <a name="job-is-consuming-too-many-streaming-units"></a>Задание потребляет слишком много единиц потоковой передачи
Воспользуйтесь преимуществами параллелизма в Azure Stream Analytics. Сведения о масштабировании с использованием заданий параллелизации запросов Stream Analytics с помощью настройки входных разделов и определения запроса аналитики см. в статье [Использование параллелизации запросов в Azure Stream Analytics](stream-analytics-parallelization.md).

## <a name="debug-queries-progressively"></a>Последовательная отладка запросов

При обработке данных в режиме реального времени важно знать, как выглядят данные во время запроса. Так как входные данные или шаги задания Azure Stream Analytics можно считать несколько раз, вы можете написать дополнительные инструкции SELECT INTO. Таким образом вы получаете промежуточные данные в хранилище, на основе которых можно проверить правильность данных, так же, как и с помощью *переменных просмотра* при отладке программы.

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

**Таблица temp1**
![запрос SELECT INTO Stream Analytics таблицы temp1 ](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**Таблица temp2**
![запрос SELECT INTO Stream Analytics таблицы temp2](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Как вы видите, обе таблицы содержат данные, а столбец имени в temp2 заполнен правильно. Тем не менее, так как данные еще не выведены, произошла какая-то проблема:

![Таблица output1 запроса SELECT INTO Stream Analytics без данных](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Выполнив выборку данных, можно почти определенно сказать, что проблема связана со второй операцией JOIN. Вы можете скачать ссылочные данные из большого двоичного объекта и убедиться в этом:

![Запрос SELECT INTO Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Как видно, формат GUID в этих справочных данных отличается от формата столбца в таблице temp2. Именно поэтому данные не поступили в output1, как положено.

Вы можете исправить формат данных, передать их в ссылочный большой двоичный объект и повторить попытку:

![Запрос временной таблицы SELECT INTO Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

На этот раз выходные данные форматируются и заполняются требуемым образом.

![Запрос итоговой таблицы SELECT INTO Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Справка

За дополнительной помощью обращайтесь на наш [форум Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Дальнейшие действия

* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
