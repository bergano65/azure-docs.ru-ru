---
title: Шаблоны развертывания что если (Предварительная версия)
description: Прежде чем развертывать шаблон Azure Resource Manager, определите, какие изменения будут выполнены для ресурсов.
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: b9d4150779842614a5dc284a2b3a489593fabfe1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388524"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Операция развертывания шаблона диспетчер ресурсов что если (Предварительная версия)

Перед развертыванием шаблона может потребоваться предварительный просмотр изменений, которые будут выполняться. Azure Resource Manager предоставляет операцию "что если", которая позволяет увидеть, как ресурсы изменятся при развертывании шаблона. Операция "что если" не вносит изменения в существующие ресурсы. Вместо этого он прогнозирует изменения, если заданный шаблон развернут.

> [!NOTE]
> Операция "что если" сейчас находится в предварительной версии. Чтобы использовать его, необходимо [зарегистрироваться для использования предварительной версии](https://aka.ms/armtemplatepreviews). В качестве предварительной версии результаты могут показывать, что ресурс изменится, когда фактическое изменение не произойдет. Мы работаем над сокращением этих проблем, но нам нужна ваша помощь. Сообщите эти проблемы по адресу [https://aka.ms/whatifissues](https://aka.ms/whatifissues).

Вы можете использовать операцию "что если" с командами PowerShell или операциями REST API.

В PowerShell выходные данные содержат кодированные цветом результаты, которые помогают увидеть различные типы изменений.

![диспетчер ресурсов развертывания шаблона что такое операция фуллресаурцепайлоад и изменение типов](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Текст ауптпут:

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

Для операции "что если" можно использовать либо Azure PowerShell, либо REST API Azure.

### <a name="azure-powershell"></a>Azure PowerShell

Чтобы просмотреть изменения перед развертыванием шаблона, добавьте параметр `-Whatif` Switch в команду развертывания.

* `New-AzResourceGroupDeployment -Whatif` развертываний группы ресурсов
* `New-AzSubscriptionDeployment -Whatif` и `New-AzDeployment -Whatif` для развертываний на уровне подписки

Можно также использовать параметр `-Confirm` Switch для предварительного просмотра изменений и получения запроса на продолжение развертывания.

* `New-AzResourceGroupDeployment -Confirm` развертываний группы ресурсов
* `New-AzSubscriptionDeployment -Confirm` и `New-AzDeployment -Confirm` для развертываний на уровне подписки

Приведенные выше команды возвращают текстовую сводку, которую можно проверить вручную. Чтобы получить объект, который можно проверить на наличие изменений программными средствами, используйте:

* `$results = Get-AzResourceGroupDeploymentWhatIf` развертываний группы ресурсов
* `$results = Get-AzSubscriptionDeploymentWhatIf` или `$results = Get-AzDeploymentWhatIf` для развертываний на уровне подписки

> [!NOTE]
> Перед выпуском версии 2.0.1-alpha5 вы использовали команду `New-AzDeploymentWhatIf`. Эта команда была заменена командами `Get-AzDeploymentWhatIf`, `Get-AzResourceGroupDeploymentWhatIf`и `Get-AzSubscriptionDeploymentWhatIf`. Если вы использовали более раннюю версию, необходимо обновить этот синтаксис. Параметр `-ScopeType` был удален.

### <a name="azure-rest-api"></a>REST API Azure

Для REST API используйте:

* [Развертывания — What If](/rest/api/resources/deployments/whatif) развертываний группы ресурсов
* [Развертывания — What If в области подписки](/rest/api/resources/deployments/whatifatsubscriptionscope) для развертываний на уровне подписки

## <a name="change-types"></a>Изменение типов

Операция "что если" перечисляет шесть различных типов изменений:

- **Создать**: ресурс в настоящее время не существует, но определен в шаблоне. Ресурс будет создан.

- **Delete**: этот тип изменения применяется только при использовании [полного режима](deployment-modes.md) развертывания. Ресурс существует, но не определен в шаблоне. В полном режиме ресурс будет удален. В этот тип изменений включаются только ресурсы, [поддерживающие удаление полного режима](complete-mode-deletion.md) .

- **Ignore**: ресурс существует, но не определен в шаблоне. Ресурс не будет развернут или изменен.

- **Без изменений**: ресурс существует и определен в шаблоне. Ресурс будет повторно развернут, но свойства ресурса не изменятся. Этот тип изменения возвращается, если [ресултформат](#result-format) имеет значение `FullResourcePayloads`, которое является значением по умолчанию.

- **Modify**: ресурс существует и определен в шаблоне. Ресурс будет повторно развернут, и свойства ресурса будут изменены. Этот тип изменения возвращается, если [ресултформат](#result-format) имеет значение `FullResourcePayloads`, которое является значением по умолчанию.

- **Deploy**: ресурс существует и определен в шаблоне. Ресурс будет повторно развернут. Свойства ресурса могут изменяться или не изменяться. Операция возвращает этот тип изменений, если у него недостаточно сведений, чтобы определить, изменяются ли какие либо свойства. Это условие отображается только в том случае, если [ресултформат](#result-format) имеет значение `ResourceIdOnly`.

## <a name="result-format"></a>Формат результата

Можно контролировать уровень детализации, возвращаемый о прогнозируемых изменениях. В командах развертывания (`New-Az*Deployment`) используйте параметр **-вхатифресултформат** . В командах программного объекта (`Get-Az*DeploymentWhatIf`) используйте параметр **ресултформат** .

Задайте для параметра Format значение **фуллресаурцепайлоадс** , чтобы получить список ресурсов, которые будут изменены, и сведения о свойствах, которые будут изменены. Задайте для параметра Format значение **ресаурцеидонли** , чтобы получить список ресурсов, которые будут изменены. Значение по умолчанию — **фуллресаурцепайлоадс**.  

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

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>Изменение теста

После завершения развертывания вы можете протестировать операцию "что если". На этот раз разверните [шаблон, который изменяет виртуальную сеть](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). В нем отсутствует один исходный тег, подсеть удалена, а префикс адреса изменился.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

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

В нижней части выходных данных показано, что владелец тега был удален. Префикс адреса изменился с 10.0.0.0/16 на 10.0.0.0/15. Подсеть с именем subnet001 удалена. Помните, что изменения на самом деле не были развернуты. Вы увидите предварительный просмотр изменений, которые будут происходить при развертывании шаблона.

Некоторые свойства, перечисленные как удаленные, фактически не меняются. Свойства могут быть ошибочно зарегистрированы как удаленные, если они не находятся в шаблоне, но автоматически устанавливаются во время развертывания в качестве значений по умолчанию. Этот результат считается "пропускаемым" в ответе "что если". Окончательный развернутый ресурс будет иметь значения, заданные для свойств. Как только операция "что если" завершилась, эти свойства будут отфильтрованы из результата.

## <a name="programmatically-evaluate-what-if-results"></a>Программное вычисление результатов поиска

Теперь давайте программно выберем результаты, если присвоить команде значение переменной.

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIf `
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

## <a name="confirm-deletion"></a>Подтверждение удаления

Операция "что если" поддерживает использование [режима развертывания](deployment-modes.md). Если задано значение полного режима, ресурсы, отсутствующие в шаблоне, удаляются. В следующем примере выполняется развертывание [шаблона, не имеющего ресурсов, определенных](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) в полном режиме.

Чтобы просмотреть изменения перед развертыванием шаблона, используйте параметр `-Confirm` Switch с командой развертывания. Если изменения ожидаемы, подтвердите, что развертывание должно завершиться.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

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

## <a name="next-steps"></a>Дальнейшие действия

- Если вы заметили неверные результаты в предварительной версии, то сообщите о проблемах по адресу [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- Сведения о развертывании шаблонов с помощью Azure PowerShell см. в статье [развертывание ресурсов с помощью шаблонов диспетчер ресурсов и Azure PowerShell](deploy-powershell.md).
- Сведения о развертывании шаблонов с помощью RESTFUL см. в статье [развертывание ресурсов с помощью шаблонов диспетчер ресурсов и диспетчер ресурсов REST API](deploy-rest.md).
