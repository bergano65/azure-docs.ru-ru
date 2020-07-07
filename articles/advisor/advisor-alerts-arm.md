---
title: Создание оповещений помощника Azure для новых рекомендаций с помощью шаблона диспетчер ресурсов
description: Создание оповещений помощника Azure для новой рекомендации
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: ef15891cc01d0481c6253023de262f14dce0ec81
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921081"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>Краткое руководство. Создание оповещений Azure Advisor по новым рекомендациям с помощью шаблона ARM

В этой статье показано, как настроить оповещение о новых рекомендациях из помощника по Azure с помощью шаблона Azure Resource Manager (шаблон ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Каждый раз, когда помощник Azure обнаруживает новую рекомендацию для одного из ресурсов, событие хранится в [журнале действий Azure](/azure/azure-monitor/platform/activity-logs-overview). Вы можете настроить оповещения для этих событий из помощника по Azure с помощью интерфейса создания оповещений, связанных с рекомендацией. Можно выбрать подписку и, при необходимости, группу ресурсов для указания ресурсов, для которых требуется получить оповещения.

Вы также можете определить типы рекомендаций, используя следующие свойства:

- Категория
- Уровень влияния
- Тип рекомендации

Вы также можете настроить действие, которое будет выполняться при активации оповещения:  

- Выбор существующей группы действий
- Создание новой группы действий

Дополнительные сведения о группах действий см. в разделе [Создание групп действий и управление ими на портале Azure](../azure-monitor/platform/action-groups.md).

> [!NOTE]
> В настоящее время оповещения Advisor доступны только для обеспечения высокой доступности, производительности и затрат. Рекомендации по безопасности не поддерживаются.

## <a name="prerequisites"></a>Предварительные требования

- Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
- Чтобы выполнить команды с локального компьютера, установите Azure CLI или модули Azure PowerShell. Дополнительные сведения см. [в статье установка Azure CLI](/cli/azure/install-azure-cli) и [Установка Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Изучение шаблона

Следующий шаблон создает группу действий с целевым объектом электронной почты и включает все уведомления о работоспособности службы для целевой подписки. Сохраните этот шаблон как *CreateAdvisorAlert.js*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "name": "[parameters('actionGroups_name')]",
      "apiVersion": "2019-06-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      },
      "dependsOn": []
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "name": "[parameters('activityLogAlerts_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
```

Шаблон определяет два ресурса:

- [Microsoft. Insights/Актионграупс](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft. Insights/Активитилогалертс](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Развертывание шаблона

Разверните шаблон, используя любой стандартный метод [развертывания шаблона ARM](../azure-resource-manager/templates/deploy-portal.md) , как показано в следующих примерах с помощью интерфейса командной строки и PowerShell. Замените образцы значений для **группы ресурсов**, а **EmailAddress** — соответствующими значениями для вашей среды. Имя рабочей области должно быть уникальным по отношению ко всем подпискам Azure.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Проверка развертывания

Убедитесь, что Рабочая область создана с помощью одной из следующих команд. Замените примеры значений для **группы ресурсов** значением, которое вы использовали ранее.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
```

---

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете продолжить работу с последующими краткими руководствами и статьями, эти ресурсы можно не удалять. Удалите группу ресурсов, которая удаляет правило генерации оповещений и связанные с ней ресурсы, если она больше не нужна. Удаление группы ресурсов с помощью Azure CLI или Azure PowerShell

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>Дальнейшие действия

- Изучите [обзор оповещений журнала действий](../azure-monitor/platform/alerts-overview.md) и узнайте, как получать оповещения.
- Дополнительные сведения о группах действий см. в статье [Создание групп действий и управление ими на портале Azure](../azure-monitor/platform/action-groups.md).
