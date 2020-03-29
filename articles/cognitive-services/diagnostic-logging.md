---
title: Ведение журнала диагностики
titleSuffix: Azure Cognitive Services
description: В этом руководстве содержатся пошаговые инструкции, позволяющие проводить диагностическую систему для когнитивной службы Azure. Эти журналы предоставляют богатые, частые данные о работе ресурса, который используется для идентификации проблем и отладки.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 539a35f170b2ee0c94762a30ed9376ca4a416210
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71827901"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Включить диагностическую систему для когнитивных служб Azure

В этом руководстве содержатся пошаговые инструкции, позволяющие проводить диагностическую систему для когнитивной службы Azure. Эти журналы предоставляют богатые, частые данные о работе ресурса, который используется для идентификации проблем и отладки. Прежде чем продолжить работу, необходимо иметь учетную запись Azure с подпиской по крайней мере на одну когнитивную службу, такую как [Bing Web Search,](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview) [Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)или [LUIS.](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)

## <a name="prerequisites"></a>Предварительные требования

Для обеспечения диагностической регистрации необходимо где-то хранить данные журнала. В этом учебнике используется аналитика данных по хранению и журналу Azure.

* [Хранилище Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) - Сохраняет диагностические журналы для аудита политики, статического анализа или резервного копирования. Учетная запись хранения не обязательно должна находиться в той самой подписке, в которой находится ресурс, выдающий журналы, если у пользователя, настраивающего параметр, имеется соответствующий доступ RBAC к обеим подпискам.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) - гибкий инструмент поиска журналов и аналитики, позволяющий анализировать необработанные журналы, генерируемые ресурсом Azure.

> [!NOTE]
> Доступны дополнительные параметры конфигурации. Чтобы узнать больше, смотрите [сбор и потребление данных журнала из ресурсов Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)

## <a name="enable-diagnostic-log-collection"></a>Включить сбор диагностических журналов  

Начнем с включения диагностической регистрации с помощью портала Azure.

> [!NOTE]
> Чтобы включить эту функцию с помощью PowerShell или Azure CLI, используйте инструкции, представленные в [Collect and consume, из ресурсов Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)

1. Перейдите на портал Azure. Затем найдите и выберите ресурс Cognitive Services. Например, подписка на веб-поиск Bing.   
2. Далее, из меню левой навигации, найдите **Мониторинг** и выберите **настройки диагностики.** Этот экран содержит все ранее созданные диагностические настройки для этого ресурса.
3. Если есть ранее созданный ресурс, который вы хотели бы использовать, вы можете выбрать его сейчас. В противном случае выберите **и добавьте диагностическую настройку.**
4. Введите имя для настройки. Затем выберите **Архив на учетную запись хранения** и отправить в журнал **Analytics**.
5. При настройке выберите учетную запись хранилища и рабочее пространство OMS, которые вы хотели бы использовать для хранения диагностических журналов. **Примечание:** Если у вас нет учетной записи хранилища или рабочего пространства OMS, следуйте запросам для ее создания.
6. Выберите **аудит,** **RequestResponse**и **AllMetrics**. Затем установите период хранения данных диагностического журнала. Если политика удержания установлена до нуля, события для этой категории журнала сохраняются бесконечно.
7. Нажмите **Сохранить**.

Это может занять до двух часов, прежде чем данные журнала доступны для запроса и анализа. Так что не волнуйтесь, если вы ничего не видите сразу.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Просмотр и экспорт диагностических данных из хранилища Azure

Azure Storage — это надежное решение для хранения объектов, которое оптимизировано для хранения больших объемов неструктурированных данных. В этом разделе вы научитесь запросить учетную запись хранилища для общего количества транзакций в течение 30-дневного периода времени и экспортировать данные, чтобы преуспеть.

1. На портале Azure найдите ресурс хранения Azure, созданный в последнем разделе.
2. Из меню левой навигации найдите **мониторинг** и выберите **метрики.**
3. Используйте доступные выпадающие места для настройки запроса. В этом примере давайте установить диапазон времени для **последних 30 дней** и метрику **транзакций.**
4. Когда запрос будет завершен, вы увидите визуализацию транзакции за последние 30 дней. Для экспорта этих данных используйте кнопку **«Экспорт в Excel»,** расположенную в верхней части страницы.

Узнайте больше о том, что можно сделать с диагностическими данными в [Azure Storage.](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)

## <a name="view-logs-in-log-analytics"></a>Просмотр журналов в Log Analytics

Следуйте этим инструкциям, чтобы изучить данные аналитики журналов для вашего ресурса.

1. На портале Azure найдите и выберите **Log Analytics** из меню левой навигации.
2. Найдите и выберите ресурс, созданный при включении диагностики.
3. Под **общим**, найти и выбрать **журналы**. С этой страницы можно запускать запросы в отношении журналов.

### <a name="sample-queries"></a>Примеры запросов

Вот несколько основных запросов Kusto, которые можно использовать для изучения данных журнала.

Выполнить этот запрос для всех диагностических журналов из когнитивных служб Azure в течение определенного периода времени:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Выполнить этот запрос, чтобы просмотреть 10 последних журналов:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Запустите этот запрос для групповых операций **ресурсом:**

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Выполнить этот запрос, чтобы найти среднее время, необходимое для выполнения операции:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Запустите этот запрос для просмотра объема операций с течением времени, разделенного OperationName, с подсчетами, сbinned для каждых 10s.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы понять, как включить журнал, а также категории метрик и журналов, поддерживаемые различными службами Azure, прочитайте [как обзор метрик](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) в статьях Microsoft Azure, так и обзор статей [Azure Diagnostic Logs.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)
* Прочтите эти статьи, чтобы узнать о концентраторах событий:
  * [Что такое Центры событий Azure?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Приступая к работе с Центрами событий](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Ознакомьтесь с разделом [скачивании метрик и журналов диагностики из службы хранилища Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Читайте [поиск журналов «Понимание» в журналах Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).
