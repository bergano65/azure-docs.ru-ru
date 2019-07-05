---
title: Настройка параметров правил интеллектуального обнаружения Azure Application Insights с помощью шаблонов Azure Resource Manager | Документация Майкрософт
description: Автоматизация настройки правил интеллектуального обнаружения Azure Application Insights и управления ими с помощью шаблонов Azure Resource Manager
services: application-insights
documentationcenter: ''
author: harelbr
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 6bb89eec0b4905e101bed87d3d3fc617dec589e0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477858"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Управление правилами интеллектуального обнаружения Application Insights с помощью шаблонов Azure Resource Manager

Для настройки правил интеллектуального обнаружения и управления ими в Application Insights можно использовать [шаблоны Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
Этот метод можно использовать при развертывании новых ресурсов Application Insights с помощью средств автоматизации Azure Resource Manager или для изменения параметров имеющихся ресурсов.

## <a name="smart-detection-rule-configuration"></a>Настройка правила интеллектуального обнаружения

Для правила интеллектуального обнаружения можно настроить следующие параметры:
- Включено ли правило (по умолчанию используется значение **true**.)
- Если сообщения электронной почты должны отправляться пользователям, связанной с подпиской [Monitoring Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) и [Monitoring Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) роли при обнаружении это обозначение (по умолчанию используется **true**.)
- Можно указать дополнительных получателей, которые должны получать уведомление при обнаружении.
    -  Настройки электронной почты не поддерживается, для правила интеллектуального обнаружения, имеющие _предварительной версии_.

Чтобы разрешить настройку параметров правил с помощью Azure Resource Manager, конфигурация правила интеллектуального обнаружения теперь доступна в качестве внутреннего ресурса в ресурсе Application Insights и называется **ProactiveDetectionConfigs**.
Чтобы достичь максимальной гибкости, для каждого правила интеллектуального обнаружения можно настроить уникальные параметры уведомлений.

## 

## <a name="examples"></a>Примеры

Ниже приведено несколько примеров, в которых показано, как настроить параметры правила интеллектуального обнаружения с помощью шаблонов Azure Resource Manager.
Во всех примерах используется ресурс Application Insights с именем _myApplication_ и правило интеллектуального обнаружения длительного срока зависимости с внутренним именем _longdependencyduration_.
Обязательно замените имя ресурса Application Insights и укажите соответствующее внутреннее имя правила интеллектуального обнаружения. В таблице ниже приведен список соответствующих внутренних имен Azure Resource Manager для каждого правила интеллектуального обнаружения.

### <a name="disable-a-smart-detection-rule"></a>Отключение правила интеллектуального обнаружения

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Отключение отправки уведомлений по электронной почте для правила интеллектуального обнаружения

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Добавление дополнительных получателей сообщений для правила интеллектуального обнаружения

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

### <a name="failure-anomalies-v2-non-classic-alert-rule"></a>Правило генерации оповещений (неклассической) версии 2 аномалии сбоя

Этот шаблон Azure Resource Manager демонстрирует настройке правила генерации оповещений аномалии в сбоях v2 с уровнем серьезности 2. Эту новую версию правила генерации оповещений аномалии в сбоях является частью нового оповещения платформы Azure и заменяет классической версии, который выводится из эксплуатации как часть [классических оповещений процесс прекращения использования](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "properties": {
                  "description": "Detects a spike in the failure rate of requests or dependencies",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Этот шаблон Azure Resource Manager является уникальным для правила генерации оповещений v2 аномальных сбоев и отличается от классической другие правила интеллектуального обнаружения описывается в этой статье.   

## <a name="smart-detection-rule-names"></a>Имена правил интеллектуального обнаружения

Ниже приведена таблица с именами правил интеллектуального обнаружения так, как они отображаются на портале, вместе с их внутренними именами, которые должны использоваться в шаблоне Azure Resource Manager.

> [!NOTE]
> Правила интеллектуального обнаружения, имеющие _предварительной версии_ не поддерживают уведомления по электронной почте. Таким образом, можно задать только _включена_ свойства для этих правил. 

| Имя правила на портале Azure | Внутреннее имя
|:---|:---|
| Длительное время загрузки страниц | slowpageloadtime |
| Низкое время отклика сервера | slowserverresponsetime |
| Длительный срок зависимости | longdependencyduration |
| Увеличение времени отклика сервера | degradationinserverresponsetime |
| Ухудшение в длительности зависимости | degradationindependencyduration |
| Ухудшение соотношения серьезности трассировок (предварительная версия) | extension_traceseveritydetector |
| Чрезмерное увеличение числа исключений (предварительная версия) | extension_exceptionchangeextension |
| Обнаружена возможная утечка памяти (предварительная версия) | extension_memoryleakextension |
| Обнаружена возможная проблема с безопасностью (предварительная версия) | extension_securityextensionspackage |
| Чрезмерное увеличение ежедневным объемом данных (Предварительная версия) | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения об автоматическом обнаружении.

- [Аномальные сбои](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Утечки памяти](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Аномалии производительности](../../azure-monitor/app/proactive-performance-diagnostics.md)
