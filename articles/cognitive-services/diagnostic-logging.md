---
title: Ведение журнала диагностики
titleSuffix: Azure Cognitive Services
description: Это руководство содержит пошаговые инструкции по включению ведения журнала диагностики для службы Azure Cognitive. Эти журналы содержат подробные и своевременные данные о работе этого ресурса, которые используются для идентификации проблемы и отладки.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: e1a6a44d7ff9d5786388fc47245ef5c79cb9be82
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155734"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Включить ведение журнала диагностики для Azure Cognitive Services

Это руководство содержит пошаговые инструкции по включению ведения журнала диагностики для службы Azure Cognitive. Эти журналы содержат подробные и своевременные данные о работе этого ресурса, которые используются для идентификации проблемы и отладки. Прежде чем продолжить, необходимо иметь учетную запись Azure с подпиской на по крайней мере один когнитивные службы, такие как [поиск в Интернете Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview), [служб речи](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview), или [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Технические условия

Чтобы включить ведение журнала диагностики, необходимо место для хранения данных журнала. В этом учебнике используется служба хранилища Azure и Log Analytics.

* [Служба хранилища Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) -сохраняет журналы диагностики для политики аудита, статического анализа или резервного копирования. Учетная запись хранения не обязательно должна находиться в той самой подписке, в которой находится ресурс, выдающий журналы, если у пользователя, настраивающего параметр, имеется соответствующий доступ RBAC к обеим подпискам.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) -средство поиска и аналитики гибкие журнала, которое позволяет для анализа необработанных журналов, создаваемых в ресурсе Azure.

> [!NOTE]
> Доступны дополнительные параметры конфигурации. Дополнительные сведения см. в разделе [сбор и использование данных журнала из ресурсов Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-diagnostic-log-collection"></a>Включите сбор журналов диагностики  

Давайте начнем, включив ведение журналов с помощью портала Azure диагностики.

> [!NOTE]
> Чтобы включить эту функцию с помощью PowerShell или Azure CLI, используйте инструкции, предоставленные в [сбор и использование данных журнала из ресурсов Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

1. Перейдите на портал Azure. Затем найдите и выберите ресурс Cognitive Services. Например, от подписки на поиск в Интернете Bing.   
2. Затем в меню навигации слева найдите **мониторинг** и выберите **параметров диагностики**. Этот экран содержит все ранее созданный параметров диагностики для данного ресурса.
3. Если имеется ранее созданный ресурс, который вы хотите использовать, можно выбрать его сейчас. В противном случае выберите **+ добавить параметр диагностики**.
4. Введите имя для параметра. Затем выберите **архивировать в учетной записи хранения** и **отправить в log Analytics**.
5. Когда будет предложено настроить, выберите учетную запись хранения и рабочей области OMS, который вы хотите использовать для хранения вы журналов диагностики. **Примечание**. Если у вас нет учетной записи хранения или рабочей области OMS, следуйте инструкциям на экране, чтобы создать его.
6. Выберите **аудита**, **RequestResponse**, и **AllMetrics**. Задайте срок хранения данных журнала диагностики. Если политику хранения равно нулю, события для такой категории журналов хранятся неограниченное время.
7. Выберите команду **Сохранить**.

Может занять до двух часов, прежде чем станет возможным запроса и анализа данных журналов. Поэтому не нужно беспокоиться, если вы не видите что-либо прямо сейчас.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Просмотр и экспорт данных диагностики из службы хранилища Azure

Хранилище Azure — это надежная объектная система хранения, которая оптимизирована для хранения больших объемов неструктурированных данных. В этом разделе вы научитесь запрашивать вашей учетной записи хранения для всего транзакций за 30-дневной период времени и экспортировать данные в excel.

1. На портале Azure найдите ресурс службы хранилища Azure, созданный в предыдущем разделе.
2. В меню навигации слева найдите **мониторинг** и выберите **метрики**.
3. Используйте доступные раскрывающиеся списки для настройки запроса. Например, давайте установите временной диапазон **последние 30 дней** и метрику **транзакции**.
4. По завершении запроса вы увидите визуализации транзакции за последние 30 дней. Чтобы экспортировать эти данные, используйте **Экспорт в Excel** расположенную в верхней части страницы.

Дополнительные сведения о возможностях диагностических данных в [хранилища Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Просмотр журналов в Log Analytics

Следуйте этим инструкциям, чтобы изучить данные log analytics для ресурса.

1. На портале Azure найдите и выберите **Log Analytics** в меню навигации слева.
2. Найдите и выберите ресурс, созданный при включении системы диагностики.
3. В разделе **Общие**, найдите и выберите **журналы**. На этой странице можно выполнять запросы от журналов.

### <a name="sample-queries"></a>Примеры запросов

Ниже приведены несколько основных запросов Kusto, которые можно использовать для изучения данных журнала.

Выполните этот запрос для всех журналов диагностики в Azure Cognitive Services в течение указанного времени.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Выполните этот запрос, чтобы увидеть 10 последних журналов:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Выполните этот запрос для группы операций, выполненных **ресурсов**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Выполните этот запрос, чтобы найти среднее время, необходимое для выполнения операции.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Выполните этот запрос для просмотра объема операций со временем разделено Имя_операции с учетом количества binned для каждого 10s.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы понять, как включить ведение журнала и метрики и категории журналов поддерживаются различными службами Azure, прочитайте [Обзор метрик](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) в Microsoft Azure и [Обзор журналов диагностики Azure ](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) статей.
* Прочтите эти статьи, чтобы узнать о концентраторах событий:
  * [Что такое Центры событий Azure?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Начало работы с Центрами событий](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Ознакомьтесь с разделом [скачивании метрик и журналов диагностики из службы хранилища Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Чтение [общие принципы по журналам в Azure Monitor журналы](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).
