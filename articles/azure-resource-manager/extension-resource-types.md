---
title: Типы ресурсов расширения Azure
description: Список типов ресурсов Azure, используемых для расширения возможностей других типов ресурсов.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: tomfitz
ms.openlocfilehash: 00e8efaaa81ec0ce189d8cfe8a78285ded445c68
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901928"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Типы ресурсов, расширяющие возможности других ресурсов

Ресурс расширения — это ресурс, который добавляет к возможностям другого ресурса. Например, блокировка ресурса является ресурсом расширения. Блокировка ресурса применяется к другому ресурсу, чтобы предотвратить его удаление или изменение. Не имеет смысла создавать блокировку ресурса самим по себе. Ресурс расширения всегда применяется к другому ресурсу.

## <a name="extension-resource-types"></a>Типы ресурсов расширения

- Microsoft. Advisor и конфигурации
- Microsoft. Advisor/рекомендации
- Microsoft. Advisor и подавления
- Microsoft. Алертсманажемент/Alerts
- Microsoft. Алертсманажемент/Алертссуммари
- Microsoft. Authorization/checkAccess
- Microsoft. Authorization/Деняссигнментс
- Microsoft. Authorization/locks
- Microsoft. Authorization/разрешения
- Microsoft. Authorization/Полициассигнментс
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/Полицисетдефинитионс
- Microsoft. Authorization/roleAssignments
- Microsoft. Authorization/roleDefinitions
- Microsoft. Billing/Биллингпериодс
- Microsoft. Billing/Биллингпермиссионс
- Microsoft. Billing/Биллингролеассигнментс
- Microsoft. Billing/Биллингроледефинитионс
- Microsoft. Billing/Креатебиллингролеассигнмент
- Microsoft. чертеж/Блуепринтассигнментс
- Microsoft. чертеж/чертежи
- Microsoft. потребление/Аггрегатедкост
- Microsoft. потребление/балансы
- Microsoft. потребление/бюджеты
- Microsoft. потребление/расходы
- Microsoft. потребление/Косттагс
- Microsoft. потребление/прогнозы
- Microsoft. потребление/Marketplace
- Microsoft. потребление/Оператионресултс
- Microsoft. потребление/значение operationstatus
- Microsoft. потребление/Прицешитс
- Microsoft. потребление/Ресерватиондетаилс
- Microsoft. потребление/Ресерватионрекоммендатионс
- Microsoft. потребление/Ресерватионсуммариес
- Microsoft. потребление/Ресерватионтрансактионс
- Microsoft. потребление/Теги
- Microsoft. потребление/условия
- Microsoft. потребление/UsageDetails
- Microsoft. потребление/кредиты
- Microsoft. потребление/события
- Microsoft. потребление/лоты
- Microsoft. потребление/продукты
- Microsoft. потребление/клиенты
- Microsoft. Контаинеринстанце/СервицеассоЦиатионлинкс
- Microsoft. Костманажемент/Alerts
- Microsoft. Костманажемент/бюджеты
- Microsoft. Костманажемент/измерения
- Microsoft. Костманажемент/EXPORTS
- Microsoft. Костманажемент/Екстерналсубскриптионс
- Microsoft. Костманажемент/прогноз
- Microsoft. Костманажемент/запрос
- Microsoft. Костманажемент/Репортконфигс
- Microsoft. Костманажемент/отчеты
- Microsoft. Костманажемент/представления
- Microsoft. Костманажемент/Шовбаккрулес
- Microsoft. Кустомпровидерс/Associations
- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics
- Microsoft. Гуестконфигуратион/Конфигуратионпрофилеассигнментс
- Microsoft. Гуестконфигуратион/Гуестконфигуратионассигнментс
- Microsoft. Гуестконфигуратион/Software
- Microsoft. Гуестконфигуратион/Софтвареупдатепрофиле
- Microsoft. Гуестконфигуратион/Софтвареупдатес
- Microsoft. Insights/Аутоматедекспортсеттингс
- Microsoft. Insights/базовый план
- Microsoft. Insights/калкулатебаселине
- Microsoft. Insights/diagnosticSettings
- Microsoft. Insights/Диагностиксеттингскатегориес
- Microsoft. Insights/евенттипес
- Microsoft. Insights/Екстендеддиагностиксеттингс
- Microsoft. Insights/ГуестдиагностиксеттингсассоЦиатион
- Microsoft. Insights/Логдефинитионс
- Microsoft. Insights/журналы
- Microsoft. Insights/metricDefinitions
- Microsoft. Insights/Метрикнамеспацес
- Microsoft. Insights/метрикбаселинес
- Microsoft. Insights/метрики
- Microsoft. Insights/Миворкбукс
- Microsoft. Insights/Вминсигхтсонбоардингстатусес
- Microsoft. Кубернетесконфигуратион/Саурцеконтролконфигуратионс
- Microsoft. Maintenance/Апплюпдатес
- Microsoft. Maintenance/Конфигуратионассигнментс
- Microsoft. Обслуживание и обновления
- Microsoft. ManagedIdentity/удостоверения
- Microsoft. ManagedServices/Регистратионассигнментс
- Microsoft. ManagedServices/Регистратиондефинитионс
- Microsoft. OperationalInsights/Сторажеинсигхтконфигс
- Microsoft. OperationsManagement/манажементассоЦиатионс
- Microsoft. Полициинсигхтс/Полициевентс
- Microsoft. Полициинсигхтс/Полицистатес
- Microsoft. Полициинсигхтс/Полицитраккедресаурцес
- Microsoft. Полициинсигхтс/исправление
- Microsoft. RecoveryServices/Баккуппротектедитемс
- Microsoft. Ресаурцехеалс/Аваилабилитистатусес
- Microsoft. Ресаурцехеалс/Чилдаваилабилитистатусес
- Microsoft. Ресаурцехеалс/Чилдресаурцес
- Microsoft. Ресаурцехеалс/события
- Microsoft. Ресаурцехеалс/Импактедресаурцес
- Microsoft. Ресаурцехеалс/уведомления
- Microsoft. Resources/Links
- Microsoft. Resources/Tags
- Microsoft. Security/соответствие требованиям
- Microsoft. Security/ИнформатионпротектионполиЦиес
- Microsoft. Security/Адаптивенетворкхарденингс
- Microsoft. Security/Адванцедсреатпротектионсеттингс
- Microsoft. Security/Ассессментметадата
- Microsoft. Security/оценки
- Microsoft. Security/Комплианцересултс
- Microsoft. Security/Датаколлектионажентс
- Microsoft. Security/Датаколлектионресултс
- Microsoft. Security/Девицесекуритиграупс
- Microsoft. Security/networkData
- Microsoft. Security/Сервервулнерабилитяссессментс
- Microsoft. Секуритинсигхтс/агрегаты
- Microsoft. Секуритинсигхтс/Алертрулетемплатес
- Microsoft. Секуритинсигхтс/alertRules
- Microsoft. Секуритинсигхтс/закладки
- Microsoft. Секуритинсигхтс/cases
- Microsoft. Секуритинсигхтс/Connects
- Microsoft. Секуритинсигхтс/сущности
- Microsoft. Секуритинсигхтс/Ентитикуериес
- Microsoft. Секуритинсигхтс/Оффицеконсентс
- Microsoft. Секуритинсигхтс/параметры
- Microsoft. Софтвареплан/Хибридусебенефитс
- Microsoft. Subscription/Креатесубскриптион
- Microsoft. support/креатесуппорттиккет
- Microsoft. support/суппорттиккетс
- Microsoft. WorkloadMonitor/компоненты
- Microsoft. WorkloadMonitor/Мониторинстанцес
- Microsoft. WorkloadMonitor/monitors
- Microsoft. WorkloadMonitor/Нотификатионсеттингс

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы получить идентификатор ресурса для расширения ресурса в шаблоне Azure Resource Manager, используйте [екстенсионресаурцеид](resource-group-template-functions-resource.md#extensionresourceid).
- Пример создания ресурса расширения в шаблоне см. в разделе подписки на [События сетки событий](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
