---
title: Запрещение изменений в ресурсах Azure | Документация Майкрософт
description: Запрет на обновление или удаление критических ресурсов Azure для пользователей путем применения блокировки для всех пользователей и ролей.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 53c57e8f-741c-4026-80e0-f4c02638c98b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: tomfitz
ms.openlocfilehash: 83518825c91cdd727b3d4fb9ecc86d51dea8fc26
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649175"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Блокировка ресурсов для предотвращения непредвиденных изменений 

Администратору иногда требуется заблокировать подписку, ресурс или группу ресурсов, чтобы другие пользователи в организации не могли случайно удалить или изменить критически важные ресурсы. Можно установить уровень блокировки **CanNotDelete** или **ReadOnly**. На портале блокировки называются **Удаление** и **Только для чтения**, соответственно.

* **CanNotDelete** означает, что авторизованные пользователи смогут читать и изменять ресурс, но не смогут его удалить. 
* **ReadOnly** означает, что авторизованные пользователи смогут читать ресурс, но не смогут его удалить или обновить. Применение этой блокировки подобно ограничению авторизованных пользователей с помощью разрешений, предоставляемых для роли **Читатель**. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>Применение блокировок

При применении блокировки к родительской области все ресурсы в этой области наследуют ту же блокировку. Даже ресурсы, добавляемые позже, наследуют блокировку от родительского ресурса. Приоритет имеет наиболее строгая блокировка.

В отличие от управления доступом на основе ролей блокировки управления используются для применения ограничения для всех пользователей и ролей. Сведения о настройке разрешений для пользователей и ролей см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](../role-based-access-control/role-assignments-portal.md).

Блокировки Resource Manager применяются только к операциям, выполняемым в плоскости управления, которая включает в себя операции, передаваемые на `https://management.azure.com`. Эти блокировки не мешают ресурсам осуществлять свои собственные операции. Ограничиваются изменения ресурсов, но не операции с ними. Например блокировка ReadOnly в базе данных SQL не позволяет удалять и изменять базы данных, но он не запрещает создание, обновление и удаление данных в базе данных. Транзакции с данными разрешены, так как эти операции не отправляются в `https://management.azure.com`.

Применение уровня блокировки **ReadOnly** (Только чтение) может привести к непредвиденным результатам, так как некоторые операции, кажущиеся операциями чтения, на самом деле требуют дополнительных действий. Например, установка уровня блокировки **ReadOnly** для учетной записи хранения не позволит всем пользователям получить список ключей. Операция отображения списка ключей обрабатывается с помощью запроса POST, поскольку возвращаемые ключи могут быть доступны для операций записи. Еще один пример: установка уровня блокировки **ReadOnly** для ресурса службы приложений не позволит обозревателю сервера Visual Studio отображать файлы для ресурса, так как для их взаимодействия требуется доступ на запись.

## <a name="who-can-create-or-delete-locks-in-your-organization"></a>Кто может создавать и удалять блокировки в вашей организации
Для создания или удаления блокировок управления необходим доступ к действию `Microsoft.Authorization/*` или `Microsoft.Authorization/locks/*`. Из встроенных ролей эти действия предоставляются только **владельцу** и **администратору доступа пользователей**.

## <a name="portal"></a>Microsoft Azure
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Шаблон

При использовании шаблона Resource Manager для развертывания блокировку, вы использовать разные значения для имени и типа в зависимости от области блокировки.

При применении блокировки **ресурсов**, использовать следующие форматы:

* Имя — `{resourceName}/Microsoft.Authorization/{lockName}`
* Тип — `{resourceProviderNamespace}/{resourceType}/providers/locks`

При применении блокировки **группы ресурсов** или **подписки**, использовать следующие форматы:

* Имя — `{lockName}`
* Тип — `Microsoft.Authorization/locks`

В следующем примере показан шаблон, который позволяет создать план обслуживания приложений, веб-сайт и блокировку на веб-сайте. Блокировка — это тип ресурса для блокировки и **/providers/locks**. Имя блокировки состоит из имени ресурса, суффикса **/Microsoft.Authorization/** и имени самой блокировки.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

Пример настройки блокировку на группу ресурсов, см. в разделе [создайте группу ресурсов и закрепите его](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell
Вы можете заблокировать развернутые ресурсы с помощью Azure PowerShell, выполнив команду [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock).

Чтобы заблокировать ресурс, укажите имя и тип ресурса, а также имя группы ресурсов.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Чтобы заблокировать группу ресурсов, укажите ее имя.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Чтобы получить сведения о блокировке, выполните команду [Get-AzureRmResourceLock](/powershell/module/az.resources/get-azresourcelock). Чтобы получить все блокировки в подписке, выполните следующую команду:

```azurepowershell-interactive
Get-AzResourceLock
```

Чтобы получить все блокировки для ресурса, выполните следующую команду:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Чтобы получить все блокировки для группы ресурсов, выполните следующую команду:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Чтобы удалить блокировку, используйте эту команду:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Вы можете заблокировать развернутые ресурсы с помощью Azure CLI, выполнив команду [az lock create](/cli/azure/lock#az-lock-create).

Чтобы заблокировать ресурс, укажите имя и тип ресурса, а также имя группы ресурсов.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Чтобы заблокировать группу ресурсов, укажите ее имя.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Чтобы получить сведения о блокировке, выполните команду [az lock list](/cli/azure/lock#az-lock-list). Чтобы получить все блокировки в подписке, выполните следующую команду:

```azurecli
az lock list
```

Чтобы получить все блокировки для ресурса, выполните следующую команду:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Чтобы получить все блокировки для группы ресурсов, выполните следующую команду:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Чтобы удалить блокировку, используйте эту команду:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST API
Вы можете заблокировать развернутые ресурсы с помощью [REST API для блокировок управления](https://docs.microsoft.com/rest/api/resources/managementlocks). REST API позволяет создавать и удалять блокировки и получать информацию о существующих блокировках.

Чтобы создать блокировку, выполните следующую команду:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Областью может быть подписка, группа ресурсов или ресурс. Вы можете назначить блокировке любое имя. Api-version, использовать **2016-09-01**.

В запросе включите объект JSON, который задает свойства блокировки.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о логической организации ресурсов см. в статье [Использование тегов для организации ресурсов](resource-group-using-tags.md).
* Ограничения и соглашения можно применять внутри подписки с помощью настраиваемых политик. Дополнительные сведения см. в статье [Что такое служба "Политика Azure"](../governance/policy/overview.md).
* Инструкции по использованию Resource Manager для эффективного управления подписками в организациях см. в статье [Корпоративный каркас Azure: рекомендуемая система управления подписками](/azure/architecture/cloud-adoption-guide/subscription-governance).

