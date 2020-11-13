---
title: Восстановление неработающей учетной записи управления Azure
description: Узнайте, как исправить неработающую учетную запись управления
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: ad54b37da8a4945162b507232f33083890ec1fff
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557864"
---
# <a name="repair-a-broken-automanage-account"></a>Восстановление неработающей учетной записи автоуправления
[Учетная запись автоматического управления](./automanage-virtual-machines.md#automanage-account) — это контекст безопасности или удостоверение, под которым выполняются автоматические операции. Если вы недавно переместили подписку, содержащую учетную запись для управления, в новый клиент, вам потребуется перенастроить учетную запись управления. Чтобы перенастроить учетную запись управления, необходимо сбросить тип удостоверения и назначить соответствующие роли для учетной записи.

## <a name="step-1-reset-automanage-account-identity-type"></a>Шаг 1. Сброс типа удостоверения учетной записи для управления
Сбросьте тип удостоверения учетной записи управления, используя шаблон Azure Resource Manager (ARM) ниже. Сохраните файл локально как `armdeploy.json` или аналогичным образом. Запишите имя и расположение учетной записи для управления, так как это обязательные параметры в шаблоне ARM.

1. Создайте новое развертывание ARM с помощью приведенного ниже шаблона и используйте `identityType = None`
    * Это можно сделать с Azure CLI помощью `az deployment sub create` . Дополнительные сведения о `az deployment sub` команде см. [здесь](https://docs.microsoft.com/cli/azure/deployment/sub).
    * Это можно также сделать с помощью PowerShell, используя `New-AzDeployment` модуль. Дополнительные сведения о `New AzDeployment` модуле см. [здесь](https://docs.microsoft.com/powershell/module/az.resources/new-azdeployment).

1. Снова запустите один и тот же шаблон ARM с помощью `identityType = SystemAssigned`

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>Шаг 2. Назначение соответствующих ролей для учетной записи управления
Для учетной записи "управление ими" требуются роли участника и политики ресурсов в подписке, содержащей виртуальные машины, которыми управляет управление. Эти роли можно назначить с помощью портал Azure, шаблонов ARM или Azure CLI.

Если вы используете шаблон ARM или Azure CLI, вам потребуется идентификатор субъекта (также известный как идентификатор объекта) учетной записи для управления правами (это необязательно, если используется портал Azure). ИДЕНТИФИКАТОР субъекта (идентификатор объекта) учетной записи службы "Управление пользователями" можно найти с помощью следующих методов.

- [Azure CLI](https://docs.microsoft.com/cli/azure/ad/sp): используйте команду `az ad sp list --display-name <name of your Automanage Account>` .

- Портал Azure. Перейдите к **Azure Active Directory** и найдите учетную запись для управления ими по имени. В разделе **корпоративные приложения** выберите имя учетной записи для управления, когда она отобразится.

### <a name="azure-portal"></a>Портал Azure
1. В разделе **подписки** перейдите к подписке, содержащей управляемые виртуальные машины.
1. Перейдите к **элементу управление доступом (IAM)**.
1. Щелкните **добавить назначения ролей**.
1. Выберите роль **участник** и введите имя учетной записи управления.
1. Нажмите кнопку **Save** (Сохранить).
1. Повторите шаги 3-5, на этот раз с ролью **участник политики ресурсов** .

### <a name="arm-template"></a>Шаблон ARM
Запустите следующий шаблон ARM. Вам потребуется идентификатор участника учетной записи управления, чтобы получить идентификатор субъекта. Введите его при появлении запроса.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Azure CLI
Выполните следующие команды:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Contributor" --scope /subscriptions/<your subscription id>

az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription id>
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об управлении Azure см. [здесь](./automanage-virtual-machines.md).
