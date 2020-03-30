---
title: Блокировка ресурсов для предотвращения изменений
description: Запрет на обновление или удаление критических ресурсов Azure для пользователей путем применения блокировки для всех пользователей и ролей.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 70fb189adb634b7ac24afe7cc8b94738117da5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274012"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Блокировка ресурсов для предотвращения непредвиденных изменений

Администратору иногда требуется заблокировать подписку, ресурс или группу ресурсов, чтобы другие пользователи в организации не могли случайно удалить или изменить критически важные ресурсы. Можно установить уровень блокировки **CanNotDelete** или **ReadOnly**. На портале блокировки называются **Удаление** и **Только для чтения**, соответственно.

* **CanNotDelete** означает, что авторизованные пользователи смогут читать и изменять ресурс, но не смогут его удалить. 
* **ReadOnly** означает, что авторизованные пользователи смогут читать ресурс, но не смогут его удалить или обновить. Применение этой блокировки подобно ограничению авторизованных пользователей с помощью разрешений, предоставляемых для роли **Читатель**.

## <a name="how-locks-are-applied"></a>Применение блокировок

При применении блокировки к родительской области все ресурсы в этой области наследуют ту же блокировку. Даже ресурсы, добавляемые позже, наследуют блокировку от родительского ресурса. Приоритет имеет наиболее строгая блокировка.

В отличие от управления доступом на основе ролей блокировки управления используются для применения ограничения для всех пользователей и ролей. Сведения о настройке разрешений для пользователей и ролей см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](../../role-based-access-control/role-assignments-portal.md).

Блокировки Resource Manager применяются только к операциям, выполняемым в плоскости управления, которая включает в себя операции, передаваемые на `https://management.azure.com`. Эти блокировки не мешают ресурсам осуществлять свои собственные операции. Ограничиваются изменения ресурсов, но не операции с ними. Например, блокировка ReadOnly в Базе данных SQL не позволит вам удалить или изменить базу данных. При этом она не мешает создавать, обновлять и удалять данные в базе данных. Транзакции с данными разрешены, поскольку эти операции не отправляются в `https://management.azure.com`.

Применение **ReadOnly** может привести к неожиданным результатам, поскольку некоторые операции, которые, кажется, не изменяют ресурс, фактически требуют действий, заблокированных блокировкой. Блокировка **ReadOnly** может быть применена к ресурсу или к группе ресурсов, содержащей ресурс. Некоторые распространенные примеры операций, заблокированных блокировкой **ReadOnly:**

* Блокировка **ReadOnly** на учетной записи хранилища не позволяет всем пользователям перечислять ключи. Операция отображения списка ключей обрабатывается с помощью запроса POST, поскольку возвращаемые ключи могут быть доступны для операций записи.

* Кроме того, применение блокировки **ReadOnly** к ресурсу службы приложений не позволит обозревателю сервера Visual Studio отображать файлы для ресурса, так как для их взаимодействия требуется доступ на запись.

* Блокировка **ReadOnly** на группе ресурсов, содержащей виртуальную машину, не позволяет всем пользователям запускать или перезапускать виртуальную машину. Эти операции требуют запроса POST.

## <a name="who-can-create-or-delete-locks"></a>Кто может создавать или удалять блокировки

Для создания или удаления блокировок управления необходим доступ к действию `Microsoft.Authorization/*` или `Microsoft.Authorization/locks/*`. Из встроенных ролей только **владельцу** и **администратору пользовательского доступа** предоставляются эти действия.

## <a name="managed-applications-and-locks"></a>Управляемые приложения и блокировки

Некоторые службы Azure, такие как Azure Databricks, используют [управляемые приложения](../managed-applications/overview.md) для реализации службы. В этом случае служба создает две группы ресурсов. Одна группа ресурсов содержит обзор службы и не заблокирована. Другая группа ресурсов содержит инфраструктуру службы и заблокирована.

Если вы попытаетесь удалить группу ресурсов инфраструктуры, вы получите ошибку, в ключанивую к которому группа ресурсов заблокирована. Если вы попытаетесь удалить блокировку для группы ресурсов инфраструктуры, вы получите ошибку, в которой указывается, что блокировка не может быть удалена, поскольку она принадлежит системному приложению.

Вместо этого удалите службу, которая также удаляет группу ресурсов инфраструктуры.

Для управляемых приложений выберите развернутую службу.

![Выберите услугу](./media/lock-resources/select-service.png)

Обратите внимание, что услуга включает в себя ссылку для **группы управляемых ресурсов.** Эта группа ресурсов удерживает инфраструктуру и заблокирована. Он не может быть удален напрямую.

![Показать управляемую группу](./media/lock-resources/show-managed-group.png)

Чтобы удалить все для службы, включая заблокированную группу ресурсов инфраструктуры, выберите **Удалить** для службы.

![Удаление службы](./media/lock-resources/delete-service.png)

## <a name="azure-backups-and-locks"></a>Резервное копирование и блокировки Azure

При блокировке группы ресурсов, созданной службой резервного копирования Azure, резервные ожидания начнут сбой. Сервис поддерживает максимум 18 точек восстановления. С блокировкой **CanNotDelete** служба резервного копирования не может очистить точки восстановления. Для получения дополнительной информации [см.](../../backup/backup-azure-vm-backup-faq.md)

## <a name="portal"></a>Портал

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Шаблон

При использовании шаблона resource Manager для развертывания блокировки используются различные значения для имени и типа в зависимости от сферы блокировки.

При применении блокировки к **ресурсу**используйте следующие форматы:

* имя -`{resourceName}/Microsoft.Authorization/{lockName}`
* Тип — `{resourceProviderNamespace}/{resourceType}/providers/locks`

При применении блокировки к **группе ресурсов** или **подписке**используйте следующие форматы:

* имя -`{lockName}`
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

Например, установка блокировки в группе ресурсов [см. Создать группу ресурсов и заблокировать ее.](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)

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

Чтобы получить информацию о блокировке, используйте [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Чтобы получить все блокировки в подписке, выполните следующую команду:

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

## <a name="azure-cli"></a>Azure CLI

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

Областью может быть подписка, группа ресурсов или ресурс. Вы можете назначить блокировке любое имя. Для api-версии, используйте **2016-09-01**.

В запросе включите объект JSON, который задает свойства блокировки.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о логической организации ресурсов см. в статье [Использование тегов для организации ресурсов](tag-resources.md).
* Ограничения и соглашения можно применять внутри подписки с помощью настраиваемых политик. Для получения дополнительной [What is Azure Policy?](../../governance/policy/overview.md)информации см.
* Инструкции по использованию Resource Manager для эффективного управления подписками в организациях см. в статье [Корпоративный каркас Azure: рекомендуемая система управления подписками](/azure/architecture/cloud-adoption-guide/subscription-governance).

