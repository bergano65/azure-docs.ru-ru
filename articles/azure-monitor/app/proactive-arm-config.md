---
title: Параметры правил интеллектуального обнаружения в Azure Application Insights
description: Автоматизация настройки правил интеллектуального обнаружения Azure Application Insights и управления ими с помощью шаблонов Azure Resource Manager
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7ca4df620739b2ab55b8ba986031cc48fe87f1fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80294915"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Управление правилами интеллектуального обнаружения Application Insights с помощью шаблонов Azure Resource Manager

Для настройки правил интеллектуального обнаружения и управления ими в Application Insights можно использовать [шаблоны Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).
Этот метод можно использовать при развертывании новых ресурсов Application Insights с помощью средств автоматизации Azure Resource Manager или для изменения параметров имеющихся ресурсов.

## <a name="smart-detection-rule-configuration"></a>Настройка правила интеллектуального обнаружения

Для правила интеллектуального обнаружения можно настроить следующие параметры:
- Включено ли правило (по умолчанию используется значение **true**.)
- Если сообщения электронной почты должны отправляться пользователям, связанным с ролью " [читатель мониторинга](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) " в подписке и ролям [участников мониторинга](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) , при обнаружении обнаружения (значение по умолчанию — **true**).
- Можно указать дополнительных получателей, которые должны получать уведомление при обнаружении.
    -  Конфигурация электронной почты недоступна для правил интеллектуального обнаружения, помеченных как _Предварительная версия_.

Чтобы разрешить настройку параметров правил с помощью Azure Resource Manager, конфигурация правила интеллектуального обнаружения теперь доступна в качестве внутреннего ресурса в ресурсе Application Insights и называется **ProactiveDetectionConfigs**.
Чтобы достичь максимальной гибкости, для каждого правила интеллектуального обнаружения можно настроить уникальные параметры уведомлений.

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


## <a name="smart-detection-rule-names"></a>Имена правил интеллектуального обнаружения

Ниже приведена таблица с именами правил интеллектуального обнаружения так, как они отображаются на портале, вместе с их внутренними именами, которые должны использоваться в шаблоне Azure Resource Manager.

> [!NOTE]
> Правила интеллектуального обнаружения, помеченные как _Предварительный просмотр_ , не поддерживают уведомления по электронной почте. Таким образом, для этих правил можно задать только свойство _Enabled_ . 

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
| Аномальный рост объема данных за день (Предварительная версия) | extension_billingdatavolumedailyspikeextension |

### <a name="failure-anomalies-alert-rule"></a>Правило генерации оповещений об аномалиях сбоев

Этот шаблон Azure Resource Manager демонстрирует настройку правила генерации оповещений об ошибках с уровнем серьезности 2. Эта новая версия правила оповещения "аномалии ошибок" входит в состав новой платформы оповещений Azure и заменяет классическую версию, которая будет снята с учета в рамках процесса прекращения использования [классических предупреждений](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

> [!NOTE]
> Аномалии сбоев — это глобальная служба, поэтому расположение правила создается в глобальном расположении.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
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
> Этот шаблон Azure Resource Manager уникален для правила генерации оповещений об аномалиях сбоя и отличается от других правил интеллектуального обнаружения, описанных в этой статье. Если вы хотите управлять аномалиями сбоев вручную, это выполняется в Azure Monitor предупреждений, в то время как все другие правила интеллектуального обнаружения управляются на панели интеллектуального обнаружения в пользовательском интерфейсе.

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения об автоматическом обнаружении.

- [Аномальные сбои](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Утечки памяти](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Аномалии производительности](../../azure-monitor/app/proactive-performance-diagnostics.md)
