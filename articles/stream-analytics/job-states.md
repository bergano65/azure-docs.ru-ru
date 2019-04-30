---
title: Состояние задания Azure Stream Analytics
description: В этой статье описываются различные состояния задания Stream Analytics.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/06/2019
ms.openlocfilehash: 28e0e69d3a6a4d3a38146cbf2c49426b3b16c784
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789458"
---
# <a name="azure-stream-analytics-job-states"></a>Состояние задания Azure Stream Analytics

В любое время задание Stream Analytics может находиться в одном из четырех состояний. Состояние задания вы можете найти на странице "Обзор" задания Stream Analytics на портале Azure. 

| Состояние | Описание | Рекомендуемые действия |
| --- | --- | --- |
| **Выполнение** | Ваше задание выполняется в событиях чтения Azure, поступающих от определенных источников входных данных, обрабатывая их и записывая результаты в настроенные приемники выходных данных. | Рекомендуется отслеживать производительность задания, выполняя мониторинг ключевых метрик. Подробнее см. в разделе [Сценарии для мониторинга](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Stopped** | Ваше задание остановлено и не обрабатывает события. | Нет данных | 
| **Ухудшение производительности** | Временные ошибки имеют свойство влиять на задание. Stream Analytics немедленно попытается восстановиться после таких ошибок и вернуться в состояние "Выполнение" (в течение нескольких минут). Эти ошибки могут произойти из-за проблем с сетью, доступности других ресурсов Azure, ошибок десериализации и т. д. Состояние ухудшения производительности влияет на саму производительность задания.| Чтобы получить дополнительные сведения о причине возникновения временных ошибок, см. раздел о [журналах действий и диагностики](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs). В некоторых случаях, например ошибок десериализации, рекомендуется предпринимать корректирующее действие, чтобы убедиться, что события находятся в правильном формате. Если задание продолжает достигать максимального использования ресурса, попробуйте увеличить количество единиц хранения или выполните [параллелизацию задания](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). В других случаях, где вы не можете применить какие-либо действия, Stream Analytics попытается восстановить состояние *Выполнение*.  |
| **Сбой** | Ваше задание с обнаруженной критической ошибкой приводит к состоянию сбоя. События не считываются и не обрабатываются. Ошибки среды выполнения являются распространенной причиной заданий, которые завершаются состоянием сбоя. | Вы можете настроить оповещения, изучив [этот раздел](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal), чтобы получать уведомления при поступлении задания в состояние "Сбой". <br> <br>Вы можете отлаживать с помощью [журналов диагностики и действий](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs), чтобы определить первопричину и обратиться к проблеме.|

## <a name="next-steps"></a>Дальнейшие действия
* [Настройка оповещений для заданий Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Метрики, доступные в Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Устранение неполадок Azure Stream Analytics с помощью журналов диагностики](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
