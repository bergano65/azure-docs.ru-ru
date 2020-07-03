---
title: Состояние задания Azure Stream Analytics
description: В этой статье описываются четыре разных состояния задания Stream Analytics. Запуск, остановка, понижение уровня работоспособности и сбой.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: fc46b4f24c3c21cda190f80556373991f6c711c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82130928"
---
# <a name="azure-stream-analytics-job-states"></a>Состояние задания Azure Stream Analytics

Задание Stream Analytics может находиться в одном из четырех состояний в любое заданное время: запуск, остановка, снижение работоспособности или сбой. Состояние задания вы можете найти на странице "Обзор" задания Stream Analytics на портале Azure. 

| Состояние | Описание | Рекомендованные действия |
| --- | --- | --- |
| **Выполнение** | Ваше задание выполняется в событиях чтения Azure, поступающих от определенных источников входных данных, обрабатывая их и записывая результаты в настроенные приемники выходных данных. | Рекомендуется отслеживать производительность задания, выполняя мониторинг ключевых метрик. Подробнее см. в разделе [Сценарии для мониторинга](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Остановлена** | Ваше задание остановлено и не обрабатывает события. | Н/Д | 
| **Пониженной функциональности** | Возможны временные проблемы с входными и выходными подключениями. Эти ошибки называются временными ошибками, которые могут привести к снижению состояния работы. Stream Analytics немедленно попытается восстановиться после таких ошибок и вернуться в состояние "Выполнение" (в течение нескольких минут). Эти ошибки могут возникать из-за проблем с сетью, доступности других ресурсов Azure, ошибок десериализации и т. д. Производительность задания может быть снижена, если задание находится в состоянии деградации.| Чтобы получить дополнительные сведения о причине возникновения временных ошибок, см. раздел о [журналах действий и диагностики](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs). В некоторых случаях, например ошибок десериализации, рекомендуется предпринимать корректирующее действие, чтобы убедиться, что события находятся в правильном формате. Если задание продолжает достигать максимального использования ресурса, попробуйте увеличить количество единиц хранения или выполните [параллелизацию задания](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). В других случаях, где вы не можете применить какие-либо действия, Stream Analytics попытается восстановить состояние *Выполнение*. <br> Вы можете использовать метрику [задержки водяного знака](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) , чтобы понять, влияют ли эти временные ошибки на производительность задания.|
| **Failed** | Ваше задание с обнаруженной критической ошибкой приводит к состоянию сбоя. События не считываются и не обрабатываются. Ошибки среды выполнения являются распространенной причиной заданий, которые завершаются состоянием сбоя. | Вы можете настроить оповещения, изучив [этот раздел](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal), чтобы получать уведомления при поступлении задания в состояние "Сбой". <br> <br>Вы можете выполнять отладку с помощью [журналов действий и ресурсов](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) для обнаружения основной причины и устранения проблемы.|

## <a name="next-steps"></a>Дальнейшие шаги
* [Настройка оповещений для заданий Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Метрики, доступные в Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Устранение неполадок с помощью журналов действий и ресурсов](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
