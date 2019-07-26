---
title: Состояние задания Azure Stream Analytics
description: В этой статье описываются четыре разных состояния задания Stream Analytics. Запуск, остановка, понижение уровня работоспособности и сбой.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: c533463ff544dc315142f7fb95c34c67933f9614
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444247"
---
# <a name="azure-stream-analytics-job-states"></a>Состояние задания Azure Stream Analytics

Задание Stream Analytics может находиться в одном из четырех состояний в любое заданное время: запуск, остановка, снижение работоспособности или сбой. Состояние задания вы можете найти на странице "Обзор" задания Stream Analytics на портале Azure. 

| Область | Описание | Рекомендуемые действия |
| --- | --- | --- |
| **Выполнение** | Ваше задание выполняется в событиях чтения Azure, поступающих от определенных источников входных данных, обрабатывая их и записывая результаты в настроенные приемники выходных данных. | Рекомендуется отслеживать производительность задания, выполняя мониторинг ключевых метрик. Подробнее см. в разделе [Сценарии для мониторинга](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Stopped** | Ваше задание остановлено и не обрабатывает события. | Н/Д | 
| **Ухудшение производительности** | Возможны временные проблемы с входными и выходными подключениями. Эти ошибки называются временными ошибками, которые могут привести к снижению состояния работы. Stream Analytics немедленно попытается восстановиться после таких ошибок и вернуться в состояние "Выполнение" (в течение нескольких минут). Эти ошибки могут произойти из-за проблем с сетью, доступности других ресурсов Azure, ошибок десериализации и т. д. Состояние ухудшения производительности влияет на саму производительность задания.| Чтобы получить дополнительные сведения о причине возникновения временных ошибок, см. раздел о [журналах действий и диагностики](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs). В некоторых случаях, например ошибок десериализации, рекомендуется предпринимать корректирующее действие, чтобы убедиться, что события находятся в правильном формате. Если задание продолжает достигать максимального использования ресурса, попробуйте увеличить количество единиц хранения или выполните [параллелизацию задания](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). В других случаях, где вы не можете применить какие-либо действия, Stream Analytics попытается восстановить состояние *Выполнение*. <br> Вы можете использовать метрику [задержки водяного знака](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) , чтобы понять, влияют ли эти временные ошибки на производительность задания.|
| **Сбой** | Ваше задание с обнаруженной критической ошибкой приводит к состоянию сбоя. События не считываются и не обрабатываются. Ошибки среды выполнения являются распространенной причиной заданий, которые завершаются состоянием сбоя. | Вы можете настроить оповещения, изучив [этот раздел](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal), чтобы получать уведомления при поступлении задания в состояние "Сбой". <br> <br>Вы можете отлаживать с помощью [журналов диагностики и действий](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs), чтобы определить первопричину и обратиться к проблеме.|

## <a name="next-steps"></a>Следующие шаги
* [Настройка оповещений для заданий Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Метрики, доступные в Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Устранение неполадок Azure Stream Analytics с помощью журналов диагностики](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
