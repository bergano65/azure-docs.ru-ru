---
title: Как запустить задание azure Stream Analytics
description: В этой статье описывается, как начать работу Stream Analytics с портала Azure, PowerShell и Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: c393eb782c2ff16eb5b3e5967b39938dfe2f1534
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426462"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Как запустить задание azure Stream Analytics

Вы можете начать работу по анализу потоков Azure с помощью портала Azure, Visual Studio и PowerShell. При запуске задания вы выбираете время для начала создания вывода. Портал Azure, Visual Studio и PowerShell имеют различные методы настройки времени начала. Эти методы описаны ниже.

## <a name="start-options"></a>Параметры запуска
Для начала работы доступны три следующих варианта. Обратите внимание, что все упомянутые ниже времена указаны в [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Если TIMESTAMP BY не указан, время прибытия будет использоваться.
* **Теперь:** делает отправную точку потока событий вывода такой же, как и при запуске задания. При использовании временного оператора (например, временное окно, LAG или JOIN) Azure Stream Analytics автоматически оглядывается на данные в источнике ввода. Например, если вы запустите задание "Сейчас" и если в запросе используется 5-минутное Tumbling Window, Azure Stream Analytics будет искать данные 5 минут назад при входе.
Первое возможное событие вывода будет иметь отметку времени, равную или превышающее текущее время, и ASA гарантирует, что все события ввода, которые могут логически способствовать выходу, были учтены. Например, не генерируются частичные оконные агрегаты. Это всегда полное агрегированное значение.

* **Пользовательские**: Вы можете выбрать отправную точку вывода. Как и в случае опции **Now,** Azure Stream Analytics автоматически считывает данные до этого времени, если используется временный оператор 

* **Когда последний остановился**. Эта опция доступна, когда задание было ранее запущено, но было остановлено вручную или сбой. При выборе этой опции Azure Stream Analytics будет использовать последнее время вывода для перезагрузки задания, чтобы данные не были потеряны. Как и предыдущие параметры, Azure Stream Analytics автоматически считывает данные до этого времени, если используется временный оператор. Поскольку несколько входных разделов могут иметь разное время, используется самое раннее время остановки всех разделов, в результате чего на выходе могут быть видны некоторые дубликаты. Более подробная информация о точно-разовой обработке доступна на странице [Гарантии доставки событий.](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)


## <a name="azure-portal"></a>Портал Azure

Перейдите к работе на портале Azure и выберите **«Старт»** на странице обзора. Выберите **время начала вывода вакансии,** а затем выберите **Начало.**

Выберите один из вариантов **начала вывода вакансии.** Параметры *теперь,* *пользовательские*, и, если задание было ранее запущен, *Когда последний остановился.* Более подробную информацию об этих вариантах можно увидеть выше.

## <a name="visual-studio"></a>Visual Studio

В представлении задания выберите кнопку «Зеленая стрелка», чтобы начать задание. Установите **режим запуска вывода рабочих мест** и выберите **Start.** Статус задания изменится на **Запуск.**

Существует три варианта **для режима запуска вывода рабочих мест:** *JobStartTime*, *CustomTime*и *LastOutputEventTime*. Если это свойство отсутствует, по умолчанию *jobStartTime*. Более подробную информацию об этих вариантах можно увидеть выше.


## <a name="powershell"></a>PowerShell

Используйте следующие cmdlet, чтобы начать свою работу с помощью PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Есть три варианта для **OutputStartMode**: *JobStartTime*, *CustomTime*, и *LastOutputEventTime*. Если это свойство отсутствует, по умолчанию *jobStartTime*. Более подробную информацию об этих вариантах можно увидеть выше.

Для получения дополнительной `Start-AzStreamAnalyitcsJob` информации о cmdlet, просмотрите [ссылку Start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство по созданию задания Stream Analytics с помощью портала Azure](stream-analytics-quick-create-portal.md)
* [Быстрый запуск: Создайте задание по аналитике потоков с помощью Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Краткое руководство. Создание задания Stream Analytics с использованием инструментов Azure Stream Analytics для Visual Studio](stream-analytics-quick-create-vs.md)
