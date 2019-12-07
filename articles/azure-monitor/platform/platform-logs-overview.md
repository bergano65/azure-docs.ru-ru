---
title: Общие сведения о журналах платформы Azure | Документация Майкрософт
description: Общие сведения о журналах в Azure Monitor, которые предоставляют Многофункциональные и часто встречающиеся данные о работе ресурса Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 480c028f11de9a7c44168b217ad3553d721d01e1
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894549"
---
# <a name="overview-of-azure-platform-logs"></a>Общие сведения о журналах платформы Azure
Журналы платформы предоставляют подробные сведения о диагностике и аудите для ресурсов Azure и платформы Azure, от которых они зависят. Они создаются автоматически, хотя необходимо настроить определенные журналы платформы для перенаправления в одно или несколько назначений, которые должны быть сохранены. В этой статье приводятся общие сведения о журналах платформы, включая сведения, которые они предоставляют, а также о том, как их настроить для сбора и анализа.

## <a name="types-of-platform-logs"></a>Типы журналов платформы
В следующей таблице перечислены конкретные журналы платформы, доступные на разных уровнях Azure.

| Слой | Журналы | Описание |
|:---|:---|:---|
| Ресурсы Azure | [Журналы ресурсов](resource-logs-overview.md) | Предоставьте сведения об операциях, которые выполнялись в ресурсе Azure ( *плоскость данных*), например получение секрета из Key Vault или выполнение запроса к базе данных. Содержимое журналов ресурсов зависит от службы и типа ресурса Azure.<br>*Журналы ресурсов ранее назывались журналами диагностики.*  |
| Подписка Azure | [Журнал действий](activity-logs-overview.md) | Предоставляет подробные сведения об операциях с каждым ресурсом Azure в подписке извне (*плоскость управления*) в дополнение к обновлениям событий работоспособности службы. Для каждой подписки Azure существует один журнал действий.   |
| Клиент Azure | [Журналы Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)  | Содержит историю действий входа и аудита изменений, внесенных в Azure Active Directory для конкретного клиента.   |


![Общие сведения о журналах платформы](media/platform-logs-overview/logs-overview.png)

## <a name="viewing-platform-logs"></a>Просмотр журналов платформы
[Журнал действий](activity-log-view.md) и [Azure Active Directory журналы](../../active-directory/reports-monitoring/overview-reports.md) можно просмотреть в портал Azure. Для их просмотра необходимо отправить журналы ресурсов в [место назначения](#destinations) .


## <a name="destinations"></a>Destinations
Журналы платформы можно отправить в одно или несколько целевых объектов в следующей таблице в зависимости от требований к мониторингу. 

| Место назначения | Сценарий | Справочники |
|:---|:---|:---|:---|
| Рабочая область Log Analytics | Анализируйте журналы с помощью других данных мониторинга и используйте Azure Monitor функции, такие как запросы журнала и оповещения. | [Журналы ресурсов](resource-logs-collect-storage.md)<br>[Журнал действий](activity-log-collect.md)<br>[Журналы каталога действий Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Хранилище Azure | Архивируйте журналы для аудита, статического анализа или резервного копирования. |[Журналы ресурсов](archive-diagnostic-logs.md)<br>[Журнал действий](activity-log-export.md)<br>[Журналы каталога действий Azure](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| концентратор событий; | Потоковая передача журналов в сторонние системы ведения журналов и телеметрии.  |[Журналы ресурсов](resource-logs-stream-event-hubs.md)<br>[Журнал действий](activity-log-export.md)<br>[Журналы каталога действий Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |


## <a name="diagnostic-settings-and-log-profiles"></a>Параметры диагностики и профили журналов
Настройте назначения для журналов ресурсов и журналов Azure Active Directory, [создав параметр диагностики](diagnostic-settings.md). Настройте назначения для журнала действий, [создав профиль журнала](activity-log-export.md) или [подключив его к рабочей области log Analytics](activity-log-collect.md).

Параметры диагностики и профиль журнала определяют следующее:

- Одно или несколько назначений для отправки выбранных журналов и метрик.
- Какие категории журналов и метрики ресурса отправляются в места назначения.
- Если в качестве назначения выбрана учетная запись хранения, то как долго должна храниться каждая категория журнала.



## <a name="next-steps"></a>Дальнейшие действия

* [Дополнительные сведения о журнале действий](activity-logs-overview.md)
* [Дополнительные сведения о журналах ресурсов](resource-logs-overview.md)
* [Просмотр схемы журнала ресурсов для различных служб Azure](diagnostic-logs-schema.md)
