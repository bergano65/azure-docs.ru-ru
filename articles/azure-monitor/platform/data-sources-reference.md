---
title: Источники данных мониторинга в Azure
description: Узнайте о всех источниках данных мониторинга, доступных в Azure в настоящее время.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 4c8d14e01805ea66ff3a954c153d858bf4ecaba6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467508"
---
# <a name="consume-monitoring-data-from-azure"></a>Использование данных мониторинга из Azure

Данные мониторинга всей платформы Azure собираются в одном месте с помощью конвейера Azure Monitor, но пока, как показывает практика, в этом конвейере доступны не все данные мониторинга. В этой статье собраны различные способы программного доступа к данным мониторинга из служб Azure.

## <a name="options-for-data-consumption"></a>Варианты использования данных

| Тип данных | Категория | Поддерживаемые службы | Варианты доступа |
| --- | --- | --- | --- |
| Метрики Azure Monitor уровня платформы | Метрики | [Список доступен здесь](metrics-supported.md). | <ul><li>**REST API:** [API метрик Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Большой двоичный объект службы хранилища или концентратор событий:** [параметры диагностики](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| Метрики гостевой ОС (например, счетчики производительности) | Метрики | Виртуальные машины [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) и Linux (версии 2), [облачные службы](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). | <ul><li>**Таблица службы хранилища или большой двоичный объект:** [система диагностики Azure (Linux или Windows)](diagnostics-extension-to-storage.md)</li><li>**Концентратор событий:** [система диагностики Azure (Windows)](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| Пользовательские метрики или метрики приложения | Метрики | Любое приложение, инструментированное с помощью Application Insights. | <ul><li>**REST API:** [REST API Application Insights](https://dev.applicationinsights.io/reference)</li></ul> |
| Метрики хранения | Метрики | Хранилище Azure | <ul><li>**Таблица службы хранилища:** [Аналитика службы хранилища](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Данные об оплате | Метрики | Все службы Azure. | <ul><li>**REST API:** [API использования ресурсов Azure и API RateCard](../../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Журнал действий | События | Все службы Azure. | <ul><li>**REST API:** [API событий Azure Monitor](https://docs.microsoft.com/rest/api/monitor/eventcategories)</li><li>**Большой двоичный объект службы хранилища или концентратор событий:** [профиль журнала](activity-logs-overview.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Журналы диагностики Azure Monitor | События | [Список доступен здесь](tutorial-dashboards.md). | <ul><li>**Большой двоичный объект службы хранилища или концентратор событий:** [параметры диагностики](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| Журналы гостевой ОС (например, IIS, трассировка событий Windows, системные журналы) | События | Виртуальные машины [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) и Linux (версии 2), [облачные службы](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). | <ul><li>**Таблица службы хранилища или большой двоичный объект:** [система диагностики Azure (Linux или Windows)](diagnostics-extension-to-storage.md)</li><li>**Концентратор событий:** [система диагностики Azure (Windows)](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| Журналы службы приложений | События | Службы приложений | <ul><li>**Хранилище BLOB-объектов, файлов или табличное хранилище:** [диагностика веб-приложения](../../app-service/troubleshoot-diagnostic-logs.md)</li></ul> |
| Журналы хранилища | События | Хранилище Azure | <ul><li>**Таблица службы хранилища:** [Аналитика службы хранилища](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Оповещения центра безопасности | События | Центр безопасности Azure | <ul><li>**REST API:** [оповещения безопасности](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Отчеты Azure Active Directory | События | Azure Active Directory | <ul><li>**REST API:** [API Graph Azure Active Directory](../../active-directory/reports-monitoring/concept-reporting-api.md)</li></ul> |
| Состояние ресурсов центра безопасности | Status | [Все поддерживаемые ресурсы](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1). | <ul><li>**REST API:** [состояния системы безопасности](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Работоспособность ресурса | Status | Поддерживаемые службы | <ul><li>**REST API:** [REST API работоспособности ресурсов](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Оповещения о метриках Azure Monitor | Уведомления | [Список доступен здесь](metrics-supported.md). | <ul><li>**Веб-перехватчик**: [оповещения о метриках Azure](alerts-webhooks.md)</li></ul> |
| Оповещения журнала действий Azure Monitor | Уведомления | Все службы Azure. | <ul><li>**Веб-перехватчик**: оповещения журнала действий Azure</li></ul> |
| Уведомления об автомасштабировании | Уведомления | [Список доступен здесь](autoscale-overview.md#supported-services-for-autoscale). | <ul><li>**Веб-перехватчик**: [схема полезных данных веб-перехватчика уведомлений автомасштабирования](autoscale-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Оповещения о запросах поиска по журналам | Уведомления | Log Analytics | <ul><li>**Веб-перехватчик**: [действия веб-перехватчика для правил оповещений журнала](alerts-log-webhook.md)</li></ul> |
| Оповещения о метриках Application Insights | Уведомления | Application Insights | <ul><li>**Веб-перехватчик**: [оповещения Application Insights](../../azure-monitor/app/alerts.md)</li></ul> |
| Веб-тесты Application Insights | Уведомления | Application Insights | <ul><li>**Веб-перехватчик**: [оповещения Application Insights](../../azure-monitor/app/alerts.md)</li></ul> |

## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше о [метриках Azure Monitor](data-collection.md).
- Узнайте больше о [журнале действий Azure](activity-logs-overview.md).
- Узнайте больше о [журналах диагностики Azure](diagnostic-logs-overview.md).

