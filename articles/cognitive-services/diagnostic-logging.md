---
title: Ведение журнала диагностики
titleSuffix: Azure Cognitive Services
description: Это краткое описание содержит пошаговые инструкции по включению ведения журнала диагностики для службы "Поиск в Azure". Эти журналы предоставляют широкие, часто встречающиеся данные о работе ресурса, которые используются для идентификации и отладки проблем.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: cd380b4e2a7c05f0beedc2ab102b268aa4068f66
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516375"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Включение ведения журнала диагностики для Azure Cognitive Services

Это краткое описание содержит пошаговые инструкции по включению ведения журнала диагностики для службы "Поиск в Azure". Эти журналы предоставляют широкие, часто встречающиеся данные о работе ресурса, которые используются для идентификации и отладки проблем. Прежде чем продолжить, необходимо иметь учетную запись Azure с подпиской хотя бы для одной службы, например [Поиск в Интернете Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview), [Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)или [Luis](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Предварительные требования

Чтобы включить ведение журнала диагностики, необходимо хранить данные журнала в любом месте. В этом руководстве используется служба хранилища Azure и Log Analytics.

* Служба [хранилища Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) — хранение журналов диагностики для аудита политики, статического анализа или резервного копирования. Учетная запись хранения не обязательно должна находиться в той самой подписке, в которой находится ресурс, выдающий журналы, если у пользователя, настраивающего параметр, имеется соответствующий доступ RBAC к обеим подпискам.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) — гибкий инструмент поиска по журналам и анализа, позволяющий анализировать необработанные журналы, созданные ресурсом Azure.

> [!NOTE]
> Доступны дополнительные параметры конфигурации. Дополнительные сведения см. в статье [Получение и использование данных журнала из ресурсов Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="enable-diagnostic-log-collection"></a>Включить сбор журналов диагностики  

Начнем с включения ведения журнала диагностики с помощью портал Azure.

> [!NOTE]
> Чтобы включить эту функцию с помощью PowerShell или Azure CLI, следуйте инструкциям, приведенным в статье [Получение и использование данных журнала из ресурсов Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#diagnostic-settings).

1. Перейдите на портал Azure. Затем найдите и выберите ресурс Cognitive Services. Например, подписка на Поиск в Интернете Bing.   
2. Затем в меню навигации слева перейдите к разделу **мониторинг** и выберите **параметры диагностики**. Этот экран содержит все ранее созданные параметры диагностики для этого ресурса.
3. Если вы хотите использовать ранее созданный ресурс, его можно выбрать сейчас. В противном случае выберите **+ Добавить параметр диагностики**.
4. Введите имя параметра. Затем выберите **Архив в учетной записи хранения** и **отправьте его в log Analytics**.
5. При появлении запроса на настройку выберите учетную запись хранения и рабочую область OMS, которую вы хотите использовать для хранения журналов диагностики. **Примечание**. Если у вас нет учетной записи хранения или рабочей области OMS, следуйте инструкциям на экране, чтобы создать ее.
6. Выберите **Audit**, **рекуестреспонсе**и **аллметрикс**. Затем задайте срок хранения для данных журнала диагностики. Если политика хранения имеет значение 0, события для этой категории журнала хранятся бессрочно.
7. Нажмите кнопку **Сохранить**.

Для запроса и анализа данных журнала может пройти до двух часов. Не беспокойтесь, если вы не видите ничего сразу же.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Просмотр и экспорт диагностических данных из службы хранилища Azure

Служба хранилища Azure — это надежное решение для хранения объектов, оптимизированное для хранения больших объемов неструктурированных данных. В этом разделе вы узнаете, как запрашивать учетную запись хранения для общего количества транзакций в течение 30 дней и экспортировать данные в Excel.

1. В портал Azure выберите ресурс хранилища Azure, созданный в предыдущем разделе.
2. В меню навигации слева перейдите к разделу **мониторинг** и выберите **метрики**.
3. Чтобы настроить запрос, воспользуйтесь раскрывающимися списками. Для этого примера давайте создадим диапазон времени за **последние 30 дней** , а метрику — на **Transaction**.
4. По завершении запроса вы увидите визуализацию транзакции за последние 30 дней. Чтобы экспортировать эти данные, используйте кнопку **Экспорт в Excel** , расположенную в верхней части страницы.

Узнайте больше о том, что можно делать с диагностическими данными в [службе хранилища Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Просмотр журналов в Log Analytics

Выполните эти инструкции, чтобы изучить данные log Analytics для вашего ресурса.

1. В портал Azure выберите **log Analytics** в меню навигации слева.
2. Выберите ресурс, созданный при включении диагностики.
3. В разделе **Общие**перейдите к пункту **журналы**. На этой странице можно выполнять запросы к журналам.

### <a name="sample-queries"></a>Примеры запросов

Ниже приведено несколько основных запросов Kusto, которые можно использовать для просмотра данных журнала.

Выполнить этот запрос для всех журналов диагностики из Cognitive Services Azure за указанный период времени:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Выполните этот запрос, чтобы просмотреть 10 последних журналов:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Выполните этот запрос, чтобы сгруппировать операции по **ресурсам**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Выполните этот запрос, чтобы найти среднее время, затрачиваемое на выполнение операции:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Выполните этот запрос, чтобы просмотреть объем операций с разбивкой по времени по параметру Имя_операции с количеством Binned для каждого 10.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Следующие шаги

* Чтобы понять, как включить ведение журнала, а также метрики и категории журналов, которые поддерживаются различными службами Azure, прочитайте [Обзор метрик](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) в Microsoft Azure и обзор статей о журналах [диагностики Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) .
* Прочтите эти статьи, чтобы узнать о концентраторах событий:
  * [Что такое Центры событий Azure?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Начало работы с Центрами событий](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Ознакомьтесь с разделом [скачивании метрик и журналов диагностики из службы хранилища Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Прочитайте [сведения о поиске по журналам в Azure Monitor журналах](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).
