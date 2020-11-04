---
title: Блокировка ресурсов для предотвращения изменений
description: Запрет на обновление или удаление критических ресурсов Azure для пользователей путем применения блокировки для всех пользователей и ролей.
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 57b4fecd0293c714dfd910ae2ad4866397646ce8
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340147"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Блокировка ресурсов для предотвращения непредвиденных изменений

Администратору иногда требуется заблокировать подписку, ресурс или группу ресурсов, чтобы другие пользователи в организации не могли случайно удалить или изменить критически важные ресурсы. Можно установить уровень блокировки **CanNotDelete** или **ReadOnly**. На портале блокировки называются **Удаление** и **Только для чтения** , соответственно.

* **CanNotDelete** означает, что авторизованные пользователи смогут читать и изменять ресурс, но не смогут его удалить.
* **ReadOnly** означает, что авторизованные пользователи смогут читать ресурс, но не смогут его удалить или обновить. Применение этой блокировки подобно ограничению авторизованных пользователей с помощью разрешений, предоставляемых для роли **Читатель**.

## <a name="how-locks-are-applied"></a>Применение блокировок

При применении блокировки к родительской области все ресурсы в этой области наследуют ту же блокировку. Даже ресурсы, добавляемые позже, наследуют блокировку от родительского ресурса. Приоритет имеет наиболее строгая блокировка.

В отличие от управления доступом на основе ролей блокировки управления используются для применения ограничения для всех пользователей и ролей. Дополнительные сведения о настройке разрешений для пользователей и ролей см. в статье [Управление доступом на основе ролей в Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).

Блокировки Resource Manager применяются только к операциям, выполняемым в плоскости управления, которая включает в себя операции, передаваемые на `https://management.azure.com`. Эти блокировки не мешают ресурсам осуществлять свои собственные операции. Ограничиваются изменения ресурсов, но не операции с ними. Например, блокировка только для чтения на логическом сервере базы данных SQL не позволяет удалить или изменить сервер. Это не мешает создавать, обновлять или удалять данные в базах данных на этом сервере. Транзакции с данными разрешены, так как эти операции не передаются на `https://management.azure.com`.

## <a name="considerations-before-applying-locks"></a>Прежде чем применять блокировки, учтите следующее.

Применение блокировок может привести к непредвиденным результатам, так как некоторые операции, которые, как кажется на первый взгляд, не изменяют ресурс, на самом деле требуют действий, выполнение которых невозможно из-за блокировки. Вот несколько распространенных примеров операций, которые блокируются в результате блокировки.

* Блокировка "только для чтения" **учетной записи хранения** не позволяет никому из пользователей получать списки ключей. Операция отображения списка ключей обрабатывается с помощью запроса POST, поскольку возвращаемые ключи могут быть доступны для операций записи.

* Кроме того, применение блокировки "только для чтения" к ресурсу **службы приложений** не позволит обозревателю сервера Visual Studio отображать файлы для ресурса, так как для их взаимодействия требуется доступ на запись.

* Блокировка "только для чтения" **группы ресурсов** , которая содержит **виртуальную машину** , не позволяет пользователям запускать или перезапускать виртуальную машину. Для этих операций требуется запрос POST.

* Блокировка "не удается удалить" в **группе ресурсов** не Azure Resource Manager [Автоматическое удаление развертываний](../templates/deployment-history-deletions.md) в журнале. Если вы достигли 800 развертываний в журнале, произойдет сбой развертывания.

* Блокировка "не удается удалить" в **группе ресурсов** , созданной **службой Azure Backup** , приводит к сбою резервного копирования. Служба поддерживает не более 18 точек восстановления. При наличии блокировки служба резервного копирования не может удалять точки восстановления. Более подробные сведения см. в разделе [Часто задаваемые вопросы о резервном копировании виртуальных машин Azure](../../backup/backup-azure-vm-backup-faq.md).

* Блокировка "только для чтения" в **подписке** не позволяет **Azure Advisor** работать корректно. Advisor не удается сохранять результаты запросов.

## <a name="who-can-create-or-delete-locks"></a>Кто может создавать или удалять блокировки

Для создания или удаления блокировок управления необходим доступ к действию `Microsoft.Authorization/*` или `Microsoft.Authorization/locks/*`. Из встроенных ролей эти действия предоставляются только **владельцу** и **администратору доступа пользователей**.

## <a name="managed-applications-and-locks"></a>Управляемые приложения и блокировки

Некоторые службы Azure, например Azure Databricks, используют для реализации этой службы [управляемые приложения](../managed-applications/overview.md). В этом случае служба создает две группы ресурсов. Одна группа ресурсов содержит обзор службы и не заблокирована. Другая группа ресурсов содержит инфраструктуру для этой службы и заблокирована.

При попытке удалить инфраструктурную группу ресурсов отображается ошибка с уведомлением о том, что группа ресурсов заблокирована. Если попытаться удалить блокировку инфраструктурной группы ресурсов, отображается ошибка с уведомлением о том, что удалить блокировку невозможно, так как она принадлежит системному приложению.

Вместо этого необходимо удалить службу, в результате чего будет удалена и инфраструктурная группа ресурсов.

В случае с управляемыми приложениями выбирайте развернутую вами службу.

![Выберите службу](./media/lock-resources/select-service.png)

Обратите внимание, что служба содержит ссылку на **группу управляемых ресурсов**. Эта группа ресурсов содержит инфраструктуру и заблокирована. Удалить ее напрямую невозможно.

![Показать управляемую группу](./media/lock-resources/show-managed-group.png)

Чтобы удалить для службы все, включая заблокированную инфраструктурную группу ресурсов, выберите **Удалить** для этой службы.

![Удаление службы](./media/lock-resources/delete-service.png)

## <a name="configure-locks"></a>Настройка блокировок

### <a name="portal"></a>Портал

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

### <a name="arm-template"></a>Шаблон ARM

Если для развертывания блокировки используется шаблон Resource Manager, то в зависимости от области блокировки назначаются разные значения имени и типа.

Если блокировка применяется к **ресурсу** , используйте следующие форматы:

* имя — `{resourceName}/Microsoft.Authorization/{lockName}`;
* тип — `{resourceProviderNamespace}/{resourceType}/providers/locks`.

При применении блокировки к **группе ресурсов** или **подписке** используйте следующие форматы:

* имя — `{lockName}`;
* тип — `Microsoft.Authorization/locks`.

В следующем примере показан шаблон, который позволяет создать план обслуживания приложений, веб-сайт и блокировку на веб-сайте. Блокировка — это тип ресурса для блокировки и **/providers/locks**. Имя блокировки состоит из имени ресурса, суффикса **/Microsoft.Authorization/** и имени самой блокировки.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2019-08-01",
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
      "type": "Microsoft.Web/sites",
      "apiVersion": "2019-08-01",
      "name": "[variables('siteName')]",
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

Пример настройки блокировки для группы ресурсов см. в разделе [Создание и блокировка группы ресурсов](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-rg-lock-role-assignment).

### <a name="azure-powershell"></a>Azure PowerShell

Вы можете заблокировать развернутые ресурсы с помощью Azure PowerShell, выполнив команду [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock).

Чтобы заблокировать ресурс, укажите имя и тип ресурса, а также имя группы ресурсов.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Чтобы заблокировать группу ресурсов, укажите ее имя.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Для получения информации о блокировке используйте команду [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Чтобы получить все блокировки в подписке, выполните следующую команду:

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

### <a name="azure-cli"></a>Azure CLI

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

### <a name="rest-api"></a>REST API

Вы можете заблокировать развернутые ресурсы с помощью [REST API для блокировок управления](/rest/api/resources/managementlocks). REST API позволяет создавать и удалять блокировки и получать информацию о существующих блокировках.

Чтобы создать блокировку, выполните следующую команду:

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}
```

Областью может быть подписка, группа ресурсов или ресурс. Вы можете назначить блокировке любое имя. В качестве версии API используйте **2016-09-01**.

В запросе включите объект JSON, который задает свойства блокировки.

```json
{
  "properties": {
  "level": "CanNotDelete",
  "notes": "Optional text notes."
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о том, как логично упорядочить свои ресурсы, см. в разделе [Использование тегов для организации ресурсов](tag-resources.md).
* Ограничения и соглашения можно применять внутри подписки с помощью настраиваемых политик. Дополнительные сведения см. в статье [Что такое служба "Политика Azure"](../../governance/policy/overview.md).
* Инструкции по использованию Resource Manager для эффективного управления подписками в организациях см. в статье [Корпоративный каркас Azure: рекомендуемая система управления подписками](/azure/architecture/cloud-adoption-guide/subscription-governance).
