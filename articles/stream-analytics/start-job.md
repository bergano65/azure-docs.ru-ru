---
title: Как запустить задание Azure Stream Analytics
description: В этой статье описывается, как запустить задание Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: fb1d724907c09e2eb77930f5a235336ca8cd3a25
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886853"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Как запустить задание Azure Stream Analytics

Можно запустить задания Azure Stream Analytics с помощью портала Azure, Visual Studio и PowerShell. При запуске задания выбрано время для начала создания выходных данных задания. Портал Azure, Visual Studio и PowerShell существуют различные методы для установки времени начала. Эти методы описаны ниже.

## <a name="azure-portal"></a>Портал Azure

Перейдите к заданию в на портале Azure и выберите **запустить** на странице "Обзор". Выберите **время начала создания выходных данных задания** , а затем выберите **запустить**.

Существует три варианта для **время начала создания выходных данных задания**: *Теперь*, *Custom*, и *во время последней остановки*. Выбрав *теперь* запускает задание в настоящее время. Выбрав *пользовательских* позволяет задать настраиваемое время в прошлом или будущем для задания должно начаться. Чтобы возобновить действие остановленного задания без потери данных, выберите *во время последней остановки*.

## <a name="visual-studio"></a>Visual Studio

В представлении задания выберите кнопку с зеленой стрелкой, чтобы запустить задание. Задайте **режим запуска выходные данные задания** и выберите **запустить**. Состояние задания изменится на **под управлением**.

Существует три варианта для **режим запуска выходные данные задания**: *JobStartTime*, *CustomTime*, и *LastOutputEventTime*. Если это свойство отсутствует, по умолчанию используется *JobStartTime*.

*JobStartTime* делает начальную точку выходное событие потоковой передачи, так же, как время запуска задания.

*CustomTime* начинается настраиваемое время, который указан в выходных данных *OutputStartTime* параметра.

*LastOutputEventTime* делает начальную точку выходного потока событий, так же, как последнего события время создания выходных данных.

## <a name="powershell"></a>PowerShell

Для запуска задания с помощью PowerShell, используйте следующий командлет:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Существует три варианта для **OutputStartMode**: *JobStartTime*, *CustomTime*, и *LastOutputEventTime*. Если это свойство отсутствует, по умолчанию используется *JobStartTime*.

*JobStartTime* делает начальную точку выходное событие потоковой передачи, так же, как время запуска задания.

*CustomTime* начинается настраиваемое время, который указан в выходных данных *OutputStartTime* параметра.

*LastOutputEventTime* делает начальную точку выходного потока событий, так же, как последнего события время создания выходных данных.

Дополнительные сведения о `Start-AzStreamAnalyitcsJob` командлета, представление [Справочник по Start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство по созданию задания Stream Analytics с помощью портала Azure](stream-analytics-quick-create-portal.md)
* [Краткое руководство Создание задания Stream Analytics с помощью Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Краткое руководство по созданию задания Stream Analytics с использованием инструментов Azure Stream Analytics для Visual Studio](stream-analytics-quick-create-vs.md)
