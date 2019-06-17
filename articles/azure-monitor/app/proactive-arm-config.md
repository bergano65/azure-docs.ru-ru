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
ms.date: 02/07/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 3ab50c92543615488d9ced599df433bf7e1e4061
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61461567"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Управление правилами интеллектуального обнаружения Application Insights с помощью шаблонов Azure Resource Manager

Для настройки правил интеллектуального обнаружения и управления ими в Application Insights можно использовать [шаблоны Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
Этот метод можно использовать при развертывании новых ресурсов Application Insights с помощью средств автоматизации Azure Resource Manager или для изменения параметров имеющихся ресурсов.

## <a name="smart-detection-rule-configuration"></a>Настройка правила интеллектуального обнаружения

Для правила интеллектуального обнаружения можно настроить следующие параметры:
- Включено ли правило (по умолчанию используется значение **true**.)
- Нужно ли отправлять сообщения владельцам подписки, участникам и читателям, если найдено обнаружение (по умолчанию используется значение **true**.)
- Можно указать дополнительных получателей, которые должны получать уведомление при обнаружении.
- * Настройки электронной почты не поддерживается Правилами интеллектуального обнаружения, доступными в _предварительной версии_.

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
> Правила интеллектуального обнаружения, помеченные как предварительная версия, не поддерживают уведомления по электронной почте. Таким образом, для этих правил можно задать только свойство enabled. 

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
| Обнаружена проблема использования ресурсов (предварительная версия) | extension_resourceutilizationextensionspackage |

## <a name="who-receives-the-classic-alert-notifications"></a>Кто получает (классические) оповещения?

Этот раздел относится только к классическим оповещениям интеллектуального обнаружения, он поможет вам оптимизировать уведомления так, чтобы они приходили только желаемым получателям. Чтобы больше узнать о разнице между [классическими оповещениями](../platform/alerts-classic.overview.md) и новыми возможностями оповещений, изучите [обзорную статью об оповещениях](../platform/alerts-overview.md). В настоящее время оповещения интеллектуального обнаружения поддерживают только классические оповещения. Единственным исключением является [оповещения интеллектуального обнаружения в облачных службах Azure](./proactive-cloud-services.md). Чтобы управлять оповещениями интеллектуального обнаружения в облачных службах Azure, используйте [группы действий](../platform/action-groups.md).

* Мы рекомендуем использовать определенных получателей для интеллектуального обнаружения и классических оповещений.

* Для оповещений интеллектуального обнаружения флажок **bulk/group** (массовый/груповой), если он включен, отправляет оповещения пользователям с ролями владельца, участника или читателя в подписке. Фактически, _все_ пользователи, имеющие доступ к подписке Application Insights, находятся в области действия и будут получать уведомления. 

> [!NOTE]
> При отключении используемого флажка **bulk/group** (массовый/груповой) вы не сможете отменить изменение.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения об автоматическом обнаружении.

- [Аномальные сбои](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Утечки памяти](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Аномалии производительности](../../azure-monitor/app/proactive-performance-diagnostics.md)