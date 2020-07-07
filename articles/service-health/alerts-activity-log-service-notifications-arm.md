---
title: Получение оповещений журнала действий для уведомлений службы Azure с помощью шаблона диспетчер ресурсов
description: Получайте уведомления по SMS, электронной почте или от веб-перехватчика при использовании службы Azure.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 84c888195ab7e2f3288691948706d31160393d25
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85918913"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>Краткое руководство. Создание оповещений журнала действий для уведомлений службы с помощью шаблона ARM

В этой статье показано, как настроить оповещения журнала действий для уведомлений о работоспособности службы с помощью шаблона Azure Resource Manager (шаблон ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Уведомления о работоспособности службы хранятся в [журнале действий Azure](../azure-monitor/platform/platform-logs-overview.md). При наличии большого объема информации, хранящейся в журнале действий, существует отдельный пользовательский интерфейс, облегчающий Просмотр и настройку оповещений о работоспособности службы.

Оповещения можно получать, когда Azure отправляет уведомления о работоспособности службы в вашу подписку Azure. Оповещения можно настроить на основе следующих данных.

- Класс уведомления о работоспособности службы (проблемы со службой, запланированное обслуживание, рекомендации по работоспособности).
- Затронутая подписка.
- Затронутые службы.
- Затронутые регионы.

> [!NOTE]
> Уведомления о работоспособности службы не содержат предупреждения о событиях работоспособности ресурсов.

Кроме того, можно настроить, кому должны отправляться оповещения:

- Выберите имеющуюся группу действий.
- Создайте группу действий (которую можно будет использовать для будущих оповещений).

Дополнительные сведения о группах действий см. в разделе [Создание групп действий и управление ими на портале Azure](../azure-monitor/platform/action-groups.md).

## <a name="prerequisites"></a>Предварительные требования

- Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
- Чтобы выполнить команды с локального компьютера, установите Azure CLI или модули Azure PowerShell. Дополнительные сведения см. [в статье установка Azure CLI](/cli/azure/install-azure-cli) и [Установка Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Изучение шаблона

Следующий шаблон создает группу действий с целевым объектом электронной почты и включает все уведомления о работоспособности службы для целевой подписки. Сохраните этот шаблон как *CreateServiceHealthAlert.js*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "SubHealth",
      "type": "String"
    },
    "activityLogAlerts_name": {
      "defaultValue": "ServiceHealthActivityLogAlert",
      "type": "String"
    },
    "emailAddress":{
      "type":"string"
    }
  },
  "variables": {
    "alertScope":"[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "apiVersion": "2019-06-01",
      "name": "[parameters('actionGroups_name')]",
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
      "comments": "Service Health Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
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
              "equals": "ServiceHealth"
            },
            {
              "field": "properties.incidentType",
              "equals": "Incident"
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

Разверните шаблон, используя любой стандартный метод [развертывания шаблона ARM](../azure-resource-manager/templates/deploy-portal.md) , как показано в следующих примерах с помощью интерфейса командной строки и PowerShell. Замените примеры значений для **группы ресурсов** , а **EmailAddress** — соответствующими значениями для вашей среды.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateServiceHealthAlert --resource-group my-resource-group --template-file CreateServiceHealthAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateServiceHealthAlert -ResourceGroupName my-resource-group -TemplateFile CreateServiceHealthAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Проверка развертывания

Убедитесь, что Рабочая область создана с помощью одной из следующих команд. Замените примеры значений для **группы ресурсов** значением, которое вы использовали ранее.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name ServiceHealthActivityLogAlert
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name ServiceHealthActivityLogAlert
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

- Узнайте о [рекомендациях по настройке оповещений о работоспособности служб Azure](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Узнайте, как [настроить мобильные push-уведомления для службы работоспособности служб Azure](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Узнайте, как [настроить уведомления веб-перехватчика для существующих систем управления проблемами](service-health-alert-webhook-guide.md).
- Дополнительные сведения об уведомлениях о работоспособности службы см. в [этой статье](service-notifications.md).
- Сведения об [ограничении скорости уведомлений](../azure-monitor/platform/alerts-rate-limiting.md).
- Ознакомьтесь со [схемой веб-перехватчика оповещений журнала действий](../azure-monitor/platform/activity-log-alerts-webhook.md).
- Изучите [обзор оповещений журнала действий](../azure-monitor/platform/alerts-overview.md) и узнайте, как получать оповещения.
- Дополнительные сведения о группах действий см. в статье [Создание групп действий и управление ими на портале Azure](../azure-monitor/platform/action-groups.md).
