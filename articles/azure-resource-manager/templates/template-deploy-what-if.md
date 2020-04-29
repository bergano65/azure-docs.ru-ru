---
title: Шаблоны развертывания что если (Предварительная версия)
description: Прежде чем развертывать шаблон Azure Resource Manager, определите, какие изменения будут выполнены для ресурсов.
author: mumian
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: jgao
ms.openlocfilehash: f13789912e5b801295f1f926a12db50849cd75d8
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509590"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>Операция "что-если" развертывания шаблона ARM (Предварительная версия)

Перед развертыванием шаблона Azure Resource Manager (ARM) можно просмотреть изменения, которые будут выполняться. Azure Resource Manager предоставляет операцию "что если", которая позволяет увидеть, как ресурсы изменятся при развертывании шаблона. Операция "что если" не вносит изменения в существующие ресурсы. Вместо этого он прогнозирует изменения, если заданный шаблон развернут.

> [!NOTE]
> Операция "что если" сейчас находится в предварительной версии. В качестве предварительной версии результаты могут показывать, что ресурс изменится, когда фактическое изменение не произойдет. Мы работаем над сокращением этих проблем, но нам нужна ваша помощь. Сообщите об этих проблемах по [https://aka.ms/whatifissues](https://aka.ms/whatifissues)адресу.

Операцию "что если" можно использовать с операциями Azure PowerShell, Azure CLI или REST API.

## <a name="install-powershell-module"></a>Установка модуля PowerShell

Чтобы использовать то, что в PowerShell, необходимо установить предварительную версию модуля AZ. Resources из коллекции PowerShell. Но перед установкой модуля убедитесь, что у вас есть PowerShell Core (6. x или 7. x). Если у вас есть PowerShell 5. x или более ранней [версии, обновите версию PowerShell](/powershell/scripting/install/installing-powershell). Вы не можете установить модуль предварительной версии в PowerShell 5. x или более ранней версии.

### <a name="install-preview-version"></a>Установить предварительную версию

Чтобы установить модуль предварительной версии, используйте:

```powershell
Install-Module Az.Resources -RequiredVersion 1.12.1-preview -AllowPrerelease
```

### <a name="uninstall-alpha-version"></a>Удалить альфа-версию

Если вы ранее установили альфа-версию модуля "что если", удалите этот модуль. Версия Alpha была доступна только пользователям, которые подписались на ранний просмотр. Если вы не устанавливали эту предварительную версию, этот раздел можно пропустить.

1. Запустите PowerShell от имени администратора.
1. Проверьте установленные версии модуля AZ. Resources.

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. Если у вас установлена версия с номером версии в формате **2. x. x-Alpha**, удалите эту версию.

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. Отмените регистрацию репозитория "что если", который использовался для установки предварительной версии.

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

Все готово к использованию.

## <a name="install-azure-cli-module"></a>Установка модуля Azure CLI

Чтобы использовать то, что в Azure CLI, необходимо иметь Azure CLI 2.5.0 или более поздней версии. При необходимости [установите последнюю версию Azure CLI](/cli/azure/install-azure-cli).

## <a name="see-results"></a>Просмотр результатов

При использовании функции что если в PowerShell или Azure CLI, выходные данные включают цветовые результаты, которые помогают увидеть различные типы изменений.

![диспетчер ресурсов развертывания шаблона что такое операция фуллресаурцепайлоад и изменение типов](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Текстовые выходные данные:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

## <a name="what-if-commands"></a>Команды "что если"

### <a name="azure-powershell"></a>Azure PowerShell

Чтобы просмотреть изменения перед развертыванием шаблона, добавьте параметр `-Whatif` Switch в команду развертывания.

* `New-AzResourceGroupDeployment -Whatif`для развертываний группы ресурсов
* `New-AzSubscriptionDeployment -Whatif`и `New-AzDeployment -Whatif` для развертываний на уровне подписки

Можно также использовать параметр `-Confirm` Switch для предварительного просмотра изменений и получения запроса на продолжение развертывания.

* `New-AzResourceGroupDeployment -Confirm`для развертываний группы ресурсов
* `New-AzSubscriptionDeployment -Confirm`и `New-AzDeployment -Confirm` для развертываний на уровне подписки

Приведенные выше команды возвращают текстовую сводку, которую можно проверить вручную. Чтобы получить объект, который можно проверить на наличие изменений программными средствами, используйте:

* `$results = Get-AzResourceGroupDeploymentWhatIfResult`для развертываний группы ресурсов
* `$results = Get-AzSubscriptionDeploymentWhatIfResult`или `$results = Get-AzDeploymentWhatIfResult` для развертываний на уровне подписки

### <a name="azure-cli"></a>Azure CLI

Чтобы просмотреть изменения перед развертыванием шаблона, используйте `what-if` команду с командой развертывания.

* `az deployment group what-if`для развертываний группы ресурсов
* `az deployment sub what-if`для развертываний на уровне подписки

Можно также использовать `--confirm-with-what-if` параметр для предварительного просмотра изменений и получить запрос на продолжение развертывания.

* `az deployment group create --confirm-with-what-if`для развертываний группы ресурсов
* `az deployment sub create --confirm-with-what-if`для развертываний на уровне подписки

Приведенные выше команды возвращают текстовую сводку, которую можно проверить вручную. Чтобы получить объект JSON, который можно программно проверить на наличие изменений, используйте:

* `az deployment group what-if --no-pretty-print`для развертываний группы ресурсов
* `az deployment sub what-if --no-pretty-print`для развертываний на уровне подписки

### <a name="azure-rest-api"></a>REST API Azure

Для REST API используйте:

* [Развертывания — What If](/rest/api/resources/deployments/whatif) развертываний группы ресурсов
* [Развертывания — What If в области подписки](/rest/api/resources/deployments/whatifatsubscriptionscope) для развертываний на уровне подписки

## <a name="change-types"></a>Изменение типов

Операция "что если" перечисляет шесть различных типов изменений:

- **Создать**: ресурс в настоящее время не существует, но определен в шаблоне. Ресурс будет создан.

- **Delete**: этот тип изменения применяется только при использовании [полного режима](deployment-modes.md) развертывания. Ресурс существует, но не определен в шаблоне. В полном режиме ресурс будет удален. В этот тип изменений включаются только ресурсы, [поддерживающие удаление полного режима](complete-mode-deletion.md) .

- **Ignore**: ресурс существует, но не определен в шаблоне. Ресурс не будет развернут или изменен.

- **Без изменений**: ресурс существует и определен в шаблоне. Ресурс будет повторно развернут, но свойства ресурса не изменятся. Этот тип изменения возвращается `FullResourcePayloads`, если [ресултформат](#result-format) имеет значение, которое является значением по умолчанию.

- **Modify**: ресурс существует и определен в шаблоне. Ресурс будет повторно развернут, и свойства ресурса будут изменены. Этот тип изменения возвращается `FullResourcePayloads`, если [ресултформат](#result-format) имеет значение, которое является значением по умолчанию.

- **Deploy**: ресурс существует и определен в шаблоне. Ресурс будет повторно развернут. Свойства ресурса могут изменяться или не изменяться. Операция возвращает этот тип изменений, если у него недостаточно сведений, чтобы определить, изменяются ли какие либо свойства. Это условие отображается только в том [ResultFormat](#result-format) случае, если для `ResourceIdOnly`ресултформат задано значение.

## <a name="result-format"></a>Формат результата

Вы управляете уровнем детализации, который возвращается о прогнозируемых изменениях. Имеются две возможности.

* **Фуллресаурцепайлоадс** — возвращает список ресурсов, которые будут изменены, и сведения о свойствах, которые будут изменены.
* **Ресаурцеидонли** — возвращает список ресурсов, которые будут изменены

Значение по умолчанию — **фуллресаурцепайлоадс**.

Для команд развертывания PowerShell используйте `-WhatIfResultFormat` параметр. В командах программного объекта используйте `ResultFormat` параметр.

В Azure CLI используйте параметр `--result-format`.
 
Следующие результаты показывают два разных формата выходных данных:

- Полные полезные данные ресурсов

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Только идентификатор ресурса

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Выполнить операцию "что если"

### <a name="set-up-environment"></a>Настройка среды

Чтобы узнать, как работает, давайте выполним некоторые тесты. Сначала разверните [шаблон, который создает виртуальную сеть](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Эта виртуальная сеть будет использоваться для тестирования того, как сообщения о внесении изменений сообщаются в.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>Изменение теста

После завершения развертывания вы можете протестировать операцию "что если". На этот раз вы развертываете [шаблон, который изменяет виртуальную сеть](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). В ней отсутствует один исходный тег, подсеть удалена, а префикс адреса изменился.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

Результат выглядит примерно так:

![Результат операции развертывания шаблона диспетчер ресурсов что-if](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Текстовые выходные данные:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Обратите внимание, что в верхней части выходных данных определены цвета, указывающие тип изменений.

В нижней части выходных данных показано, что владелец тега был удален. Префикс адреса изменился с 10.0.0.0/16 на 10.0.0.0/15. Подсеть с именем subnet001 удалена. Помните, что эти изменения не были развернуты на самом деле. Вы увидите предварительный просмотр изменений, которые будут происходить при развертывании шаблона.

Некоторые свойства, перечисленные как удаленные, фактически не меняются. Свойства могут быть ошибочно зарегистрированы как удаленные, если они не находятся в шаблоне, но автоматически устанавливаются во время развертывания в качестве значений по умолчанию. Этот результат считается "пропускаемым" в ответе "что если". Окончательный развернутый ресурс будет иметь значения, заданные для свойств. Как только операция "что если" завершилась, эти свойства будут отфильтрованы из результата.

## <a name="programmatically-evaluate-what-if-results"></a>Программное вычисление результатов поиска

Теперь давайте программно выберем результаты, если присвоить команде значение переменной.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Можно просмотреть сводку по каждому изменению.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>Подтверждение удаления

Операция "что если" поддерживает использование [режима развертывания](deployment-modes.md). Если задано значение полного режима, ресурсы, отсутствующие в шаблоне, удаляются. В следующем примере выполняется развертывание [шаблона, не имеющего ресурсов, определенных](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) в полном режиме.

Чтобы просмотреть изменения перед развертыванием шаблона, используйте параметр `-Confirm` Switch с командой развертывания. Если изменения ожидаемы, подтвердите, что развертывание должно завершиться.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

Так как в шаблоне не определены ресурсы, а для режима развертывания задано значение "завершено", виртуальная сеть будет удалена.

![Диспетчер ресурсов развертывание шаблона "что если" выходное режим развертывания операции завершен](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Текстовые выходные данные:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

Вы увидите ожидаемые изменения и можете подтвердить, что вы хотите выполнить развертывание.

## <a name="next-steps"></a>Следующие шаги

- Если вы заметили неверные результаты в предварительной версии, то сообщите о проблемах по адресу [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- Сведения о развертывании шаблонов с помощью Azure PowerShell см. в статье [развертывание ресурсов с помощью шаблонов ARM и Azure PowerShell](deploy-powershell.md).
- Сведения о развертывании шаблонов с помощью Azure CLI см. в статье [развертывание ресурсов с помощью шаблонов ARM и Azure CLI](deploy-cli.md).
- Сведения о развертывании шаблонов с помощью RESTFUL см. в статье [развертывание ресурсов с помощью шаблонов ARM и диспетчер ресурсов REST API](deploy-rest.md).
