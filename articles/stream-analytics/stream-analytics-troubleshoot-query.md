---
title: Устранение неполадок з запросами в службе Azure Stream Analytics
description: В этой статье описаны методы устранения неполадок с запросами в заданиях Azure Stream Analytics.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7a1e440a8dc8f518e272df9e126771df54390ed5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60762486"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Устранение неполадок з запросами в службе Azure Stream Analytics

В этой статье описаны распространенные проблемы с разработкой запросов Stream Analytics и способы их устранения.

## <a name="query-is-not-producing-expected-output"></a>Запрос не создает ожидаемых выходных данных 
1.  Изучите ошибки с помощью локального тестирования:
    - На вкладке **Запрос** выберите **Тест**. Для [проверки запроса](stream-analytics-test-query.md) используйте загруженные демонстрационные данные. Проанализируйте все ошибки и попытайтесь исправить их.   
    - Кроме того, вы можете [проверить запрос, используя реальные входные данные](stream-analytics-live-data-local-testing.md), с помощью инструментов Stream Analytics для Visual Studio.

2.  Если используются [**метки времени**](https://msdn.microsoft.com/library/azure/mt573293.aspx), убедитесь, что они зарегистрированы для событий после [начала выполнения задания](stream-analytics-out-of-order-and-late-events.md).

3.  Исключите типичные проблемы, например:
    - Предложение [ **WHERE**](https://msdn.microsoft.com/library/azure/dn835048.aspx) в запросе фильтрует все события, предотвращая создание выходных данных.
    - Выполнение функции [**CAST**](https://msdn.microsoft.com/azure/stream-analytics/reference/cast-azure-stream-analytics) завершается ошибкой, что приводит к сбою всего задания. Чтобы избежать сбоев приведения типов, используйте функцию [**TRY_CAST**](https://msdn.microsoft.com/azure/stream-analytics/reference/try-cast-azure-stream-analytics).
    - При использовании оконных функций подождите до завершения окна для вывода выходных данных из запроса.
    - Метка времени для событий предшествует времени запуска задания, поэтому события сбрасываются.

4.  Убедитесь, что политики упорядочения событий настроены надлежащим образом. Перейдите к **параметрам** и выберите [**Упорядочение событий**](stream-analytics-out-of-order-and-late-events.md). Эта политика *НЕ* применяется при проверке запроса по нажатию кнопки **Тест**. В результате тестирования в браузере и при выполнении задания в рабочей среде отличаются одним компонентом. 

5. Выполните отладку с помощью журналов аудита и диагностики:
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

## <a name="get-help"></a>Получение справки

За дополнительной помощью обращайтесь на наш [форум Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Дальнейшие действия

* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Приступая к работе с Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Масштабирование заданий в службе Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Справочник по языку запросов Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Справочник по API-интерфейсу REST управления Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)