---
title: Блокировка ресурсов для предотвращения изменений
description: Запрет на обновление или удаление критических ресурсов Azure для пользователей путем применения блокировки для всех пользователей и ролей.
ms.topic: conceptual
ms.date: 05/14/2019
ms.openlocfilehash: 5dd5d5f58e13039842dca85ca65d6a26ce54c7e5
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150781"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Блокировка ресурсов для предотвращения непредвиденных изменений

Администратору иногда требуется заблокировать подписку, ресурс или группу ресурсов, чтобы другие пользователи в организации не могли случайно удалить или изменить критически важные ресурсы. Можно установить уровень блокировки **CanNotDelete** или **ReadOnly**. На портале блокировки называются **Удаление** и **Только для чтения**, соответственно.

* **CanNotDelete** означает, что авторизованные пользователи смогут читать и изменять ресурс, но не смогут его удалить. 
* **ReadOnly** означает, что авторизованные пользователи смогут читать ресурс, но не смогут его удалить или обновить. Применение этой блокировки подобно ограничению авторизованных пользователей с помощью разрешений, предоставляемых для роли **Читатель**. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>Применение блокировок

При применении блокировки к родительской области все ресурсы в этой области наследуют ту же блокировку. Даже ресурсы, добавляемые позже, наследуют блокировку от родительского ресурса. Приоритет имеет наиболее строгая блокировка.

В отличие от управления доступом на основе ролей блокировки управления используются для применения ограничения для всех пользователей и ролей. Сведения о настройке разрешений для пользователей и ролей см. в статье [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](../role-based-access-control/role-assignments-portal.md).

Блокировки Resource Manager применяются только к операциям, выполняемым в плоскости управления, которая включает в себя операции, передаваемые на `https://management.azure.com`. Эти блокировки не мешают ресурсам осуществлять свои собственные операции. Ограничиваются изменения ресурсов, но не операции с ними. Например, блокировка ReadOnly в Базе данных SQL не позволит вам удалить или изменить базу данных. При этом она не мешает создавать, обновлять и удалять данные в базе данных. Транзакции с данными разрешены, так как эти операции не отправляются в `https://management.azure.com`.

Применение **ReadOnly** может привести к непредвиденным результатам, так как некоторые операции, которые не могут изменить ресурс, фактически нуждаются в действиях, заблокированных блокировкой. Блокировку **только для чтения** можно применить к ресурсу или к группе ресурсов, содержащей ресурс. Ниже приведены некоторые распространенные примеры операций, блокируемых блокировкой **только для чтения** .

* Блокировка **только для чтения** в учетной записи хранения запрещает всем пользователям перечисление ключей. Операция отображения списка ключей обрабатывается с помощью запроса POST, поскольку возвращаемые ключи могут быть доступны для операций записи.

* Кроме того, применение блокировки **ReadOnly** к ресурсу службы приложений не позволит обозревателю сервера Visual Studio отображать файлы для ресурса, так как для их взаимодействия требуется доступ на запись.

* Блокировка **только для чтения** в группе ресурсов, содержащей виртуальную машину, не позволяет всем пользователям запускать или перезапускать виртуальную машину. Для этих операций требуется запрос POST.

## <a name="who-can-create-or-delete-locks"></a>Кто может создавать или удалять блокировки
Для создания или удаления блокировок управления необходим доступ к действию `Microsoft.Authorization/*` или `Microsoft.Authorization/locks/*`. Из встроенных ролей эти действия предоставляются только **владельцу** и **администратору доступа пользователей**.

## <a name="managed-applications-and-locks"></a>Управляемые приложения и блокировки

Некоторые службы Azure, такие как Azure Databricks, используют [управляемые приложения](../managed-applications/overview.md) для реализации службы. В этом случае служба создает две группы ресурсов. Одна группа ресурсов содержит общие сведения о службе и не заблокирована. Другая группа ресурсов содержит инфраструктуру для службы и заблокирована.

При попытке удалить группу ресурсов инфраструктуры появляется сообщение об ошибке, указывающее, что группа ресурсов заблокирована. Если вы попытаетесь удалить блокировку для группы ресурсов инфраструктуры, появится сообщение об ошибке, сообщающее, что блокировка не может быть удалена, так как она принадлежит системному приложению.

Вместо этого удалите службу, которая также удаляет группу ресурсов инфраструктуры.

Для управляемых приложений выберите развернутую службу.

![Выбор службы](./media/resource-group-lock-resources/select-service.png)

Обратите внимание, что служба содержит ссылку на **управляемую группу ресурсов**. Эта группа ресурсов содержит инфраструктуру и заблокирована. Его нельзя удалить напрямую.

![Отображение управляемой группы](./media/resource-group-lock-resources/show-managed-group.png)

Чтобы удалить все для службы, включая группу ресурсов заблокированной инфраструктуры, выберите **Удалить** для службы.

![Удаление службы](./media/resource-group-lock-resources/delete-service.png)

## <a name="portal"></a>Портал
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>шаблона

При использовании шаблона диспетчер ресурсов для развертывания блокировки используются разные значения имени и типа в зависимости от области блокировки.

При применении блокировки к **ресурсу**используйте следующие форматы:

* имя — `{resourceName}/Microsoft.Authorization/{lockName}`
* Тип — `{resourceProviderNamespace}/{resourceType}/providers/locks`

При применении блокировки к **группе ресурсов** или **подписке**используйте следующие форматы:

* имя — `{lockName}`
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

Пример настройки блокировки для группы ресурсов см. в разделе [Создание группы ресурсов и ее блокировка](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

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

Чтобы получить сведения о блокировке, используйте [Get-азресаурцелокк](/powershell/module/az.resources/get-azresourcelock). Чтобы получить все блокировки в подписке, выполните следующую команду:

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

## <a name="azure-cli"></a>Интерфейс командной строки Azure

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

## <a name="rest-api"></a>Интерфейс REST API
Вы можете заблокировать развернутые ресурсы с помощью [REST API для блокировок управления](https://docs.microsoft.com/rest/api/resources/managementlocks). REST API позволяет создавать и удалять блокировки и получать информацию о существующих блокировках.

Чтобы создать блокировку, выполните следующую команду:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Областью может быть подписка, группа ресурсов или ресурс. Вы можете назначить блокировке любое имя. Для API-Version используйте **2016-09-01**.

В запросе включите объект JSON, который задает свойства блокировки.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о логической организации ресурсов см. в статье [Использование тегов для организации ресурсов](resource-group-using-tags.md).
* Ограничения и соглашения можно применять внутри подписки с помощью настраиваемых политик. Дополнительные сведения см. в статье [Что такое служба "Политика Azure"](../governance/policy/overview.md).
* Руководство по использованию Resource Manager для эффективного управления подписками в организациях см [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Шаблон Azure для организаций. Рекомендуемая система управления подпиской).

