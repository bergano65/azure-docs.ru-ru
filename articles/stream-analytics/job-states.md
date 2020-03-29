---
title: Состояние задания Azure Stream Analytics
description: В этой статье описаны четыре различных состояния задания Stream Analytics; работает, остановился, деградировал, и не удалось.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 454474333cac94dc25deae8196e9ba45bcb3a574
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75359772"
---
# <a name="azure-stream-analytics-job-states"></a>Состояние задания Azure Stream Analytics

Задание Stream Analytics может находиться в одном из четырех состояний в любой момент времени: запуск, остановки, деградии или неудача. Состояние задания вы можете найти на странице "Обзор" задания Stream Analytics на портале Azure. 

| Штат | Описание | Рекомендованные действия |
| --- | --- | --- |
| **Выполнение** | Ваше задание выполняется в событиях чтения Azure, поступающих от определенных источников входных данных, обрабатывая их и записывая результаты в настроенные приемники выходных данных. | Рекомендуется отслеживать производительность задания, выполняя мониторинг ключевых метрик. Подробнее см. в разделе [Сценарии для мониторинга](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Остановился** | Ваше задание остановлено и не обрабатывает события. | Н/Д | 
| **Деградированных** | Могут возникнуть периодические проблемы с входными и выходными соединениями. Эти ошибки называются временными ошибками, которые могут привести вашу работу в деградированный состояние. Stream Analytics немедленно попытается восстановиться после таких ошибок и вернуться в состояние "Выполнение" (в течение нескольких минут). Эти ошибки могут произойти из-за проблем с сетью, доступности других ресурсов Azure, ошибок десериализации и т.д. Производительность вашей работы может быть затронута, когда работа находится в деградированном состоянии.| Чтобы получить дополнительные сведения о причине возникновения временных ошибок, см. раздел о [журналах действий и диагностики](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs). В некоторых случаях, например ошибок десериализации, рекомендуется предпринимать корректирующее действие, чтобы убедиться, что события находятся в правильном формате. Если задание продолжает достигать максимального использования ресурса, попробуйте увеличить количество единиц хранения или выполните [параллелизацию задания](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). В других случаях, где вы не можете применить какие-либо действия, Stream Analytics попытается восстановить состояние *Выполнение*. <br> Вы можете использовать метрика [задержки водяного знака,](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) чтобы понять, влияют ли эти переходные ошибки на производительность вашей работы.|
| **Сбой при** | Ваше задание с обнаруженной критической ошибкой приводит к состоянию сбоя. События не считываются и не обрабатываются. Ошибки среды выполнения являются распространенной причиной заданий, которые завершаются состоянием сбоя. | Вы можете настроить оповещения, изучив [этот раздел](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal), чтобы получать уведомления при поступлении задания в состояние "Сбой". <br> <br>Вы можете отлаживать с помощью [журналов диагностики и действий](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs), чтобы определить первопричину и обратиться к проблеме.|

## <a name="next-steps"></a>Дальнейшие действия
* [Настройка оповещений для заданий Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Метрики, доступные в Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Устранение неполадок Azure Stream Analytics с помощью журналов диагностики](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
