---
title: Источники данных мониторинга в Azure
description: Узнайте о всех источниках данных мониторинга, доступных в Azure в настоящее время.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: b01dafcda9ed8fe14e3f0d50daf25bae1658faee
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282634"
---
# <a name="consume-monitoring-data-from-azure"></a>Использование данных мониторинга из Azure

Данные мониторинга всей платформы Azure собираются в одном месте с помощью конвейера Azure Monitor, но пока, как показывает практика, в этом конвейере доступны не все данные мониторинга. В этой статье собраны различные способы программного доступа к данным мониторинга из служб Azure.

## <a name="options-for-data-consumption"></a>Варианты использования данных

| Тип данных | Категория | Поддерживаемые службы | Варианты доступа |
| --- | --- | --- | --- |
| Метрики Azure Monitor уровня платформы | Метрики | [Список доступен здесь](monitoring-supported-metrics.md). | <ul><li>**REST API:** [API метрик Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metrics).</li><li>**Большой двоичный объект службы хранилища или концентратор событий:** [параметры диагностики](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).</li></ul> |
| Метрики гостевой ОС (например, счетчики производительности) | Метрики | Виртуальные машины [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) и Linux (версии 2), [облачные службы](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). | <ul><li>**Таблица или большой двоичный объект службы хранилища:** [система диагностики Azure для Windows или Linux](azure-diagnostics-storage.md).</li><li>**Концентратор событий:** [система диагностики Microsoft Azure](azure-diagnostics-streaming-event-hubs.md).</li></ul> |
| Пользовательские метрики или метрики приложения | Метрики | Любое приложение, инструментированное с помощью Application Insights. | <ul><li>**REST API:** [REST API Application Insights](https://dev.applicationinsights.io/reference).</li></ul> |
| Метрики хранения | Метрики | Хранилище Azure | <ul><li>**Таблица службы хранилища:** [аналитика службы хранилища](https://docs.microsoft.com/rest/api/storageservices/storage-analytics).</li></ul> |
| Данные об оплате | Метрики | Все службы Azure. | <ul><li>**REST API:** [использование ресурсов Azure и интерфейсы API RateCard](../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Журнал действий | События | Все службы Azure. | <ul><li>**REST API:** [API событий Azure Monitor](https://docs.microsoft.com/rest/api/monitor/eventcategories).</li><li>**Большой двоичный объект службы хранилища или концентратор событий:** [профиль журнала](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile).</li></ul> |
| Журналы диагностики Azure Monitor | События | [Список доступен здесь](monitoring-diagnostic-logs-schema.md). | <ul><li>**Большой двоичный объект службы хранилища или концентратор событий:** [параметры диагностики](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).</li></ul> |
| Журналы гостевой ОС (например, IIS, трассировка событий Windows, системные журналы) | События | Виртуальные машины [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) и Linux (версии 2), [облачные службы](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). | <ul><li>**Таблица или большой двоичный объект службы хранилища:** [система диагностики Azure для Windows или Linux](azure-diagnostics-storage.md).</li><li>**Концентратор событий:** [система диагностики Microsoft Azure](azure-diagnostics-streaming-event-hubs.md).</li></ul> |
| Журналы службы приложений | События | Службы приложений | <ul><li>**Хранилище файлов, таблиц или BLOB-объектов:** [диагностика веб-приложений](../app-service/web-sites-enable-diagnostic-log.md).</li></ul> |
| Журналы хранилища | События | Хранилище Azure | <ul><li>**Таблица службы хранилища:** [аналитика службы хранилища](https://docs.microsoft.com/rest/api/storageservices/storage-analytics).</li></ul> |
| Оповещения центра безопасности | События | Центр безопасности Azure | <ul><li>**REST API:** [оповещения системы безопасности](https://msdn.microsoft.com/library/mt704050.aspx).</li></ul> |
| Отчеты Azure Active Directory | События | Azure Active Directory | <ul><li>**REST API:** [API Graph Azure Active Directory](../active-directory/reports-monitoring/concept-reporting-api.md).</li></ul> |
| Состояние ресурсов центра безопасности | Status | [Все поддерживаемые ресурсы](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1). | <ul><li>**REST API:** [состояния системы безопасности](https://msdn.microsoft.com/library/mt704041.aspx).</li></ul> |
| Работоспособность ресурса | Status | Поддерживаемые службы | <ul><li>**REST API:** [REST API работоспособности ресурсов](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/).</li></ul> |
| Оповещения о метриках Azure Monitor | Уведомления | [Список доступен здесь](monitoring-supported-metrics.md). | <ul><li>**Webhook:** [оповещения о метриках Azure](insights-webhooks-alerts.md).</li></ul> |
| Оповещения журнала действий Azure Monitor | Уведомления | Все службы Azure. | <ul><li>**Webhook**: оповещения журнала действий Azure.</li></ul> |
| Уведомления об автомасштабировании | Уведомления | [Список доступен здесь](monitoring-overview-autoscale.md#supported-services-for-autoscale). | <ul><li>**Webhook:** [схема полезных данных webhook уведомлений об автомасштабировании](insights-autoscale-to-webhook-email.md#autoscale-notification-webhook-payload-schema).</li></ul> |
| Оповещения о запросах поиска по журналам | Уведомления | Log Analytics | <ul><li>**Webhook:** [действия веб-перехватчика для правил оповещений журнала](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md).</li></ul> |
| Оповещения о метриках Application Insights | Уведомления | Application Insights | <ul><li>**Webhook:** [оповещения Application Insights](../application-insights/app-insights-alerts.md).</li></ul> |
| Веб-тесты Application Insights | Уведомления | Application Insights | <ul><li>**Webhook:** [оповещения Application Insights](../application-insights/app-insights-alerts.md).</li></ul> |

## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше о [метриках Azure Monitor](../monitoring/monitoring-data-collection.md).
- Узнайте больше о [журнале действий Azure](monitoring-overview-activity-logs.md).
- Узнайте больше о [журналах диагностики Azure](monitoring-overview-of-diagnostic-logs.md).
