---
title: Развертывание шаблона что-если (Предварительный просмотр)
description: Определите, какие изменения произойдут с вашими ресурсами перед развертыванием шаблона управления ресурсами Azure.
author: mumian
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jgao
ms.openlocfilehash: b8e94d0b4f364e2873dfc21792a67f11c33483bf
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010195"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>Развертывание шаблона ARM что-если (Предварительный просмотр)

Перед развертыванием шаблона Azure Resource Manager (ARM) можно просмотреть изменения, которые произойдут. Менеджер ресурсов Azure предоставляет операцию what-if, чтобы вы могли видеть, как изменятся ресурсы при развертывании шаблона. Операция what-if не внесет никаких изменений в существующие ресурсы. Вместо этого он предсказывает изменения, если указанный шаблон развернут.

> [!NOTE]
> Операция what-if в настоящее время находится в предварительном просмотре. В качестве предварительного релиза результаты могут иногда показывать, что ресурс изменится, когда на самом деле никаких изменений не произойдет. Мы работаем над тем, чтобы уменьшить эти проблемы, но нам нужна ваша помощь. Пожалуйста, сообщите об этих проблемах на [https://aka.ms/whatifissues](https://aka.ms/whatifissues).

Вы можете использовать операцию «что,если» с командами PowerShell или операции REST API.

## <a name="install-powershell-module"></a>Установка модуля PowerShell

Чтобы использовать что-если в PowerShell, установите предварительную версию модуля Az.Resources из галереи PowerShell.

### <a name="install-preview-version"></a>Установка предварительной версии

Для установки модуля предварительного просмотра используйте:

```powershell
Install-Module Az.Resources -RequiredVersion 1.12.1-preview -AllowPrerelease
```

### <a name="uninstall-alpha-version"></a>Удалить альфа-версию

Если вы ранее установили альфа-версию модуля «что если», удалите этот модуль. Альфа-версия была доступна только для пользователей, которые подписались на ранний предварительный просмотр. Если вы не установили предварительный просмотр, вы можете пропустить этот раздел.

1. Запустите PowerShell от имени администратора.
1. Проверьте установленные версии модуля Az.Resources.

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. Если у вас есть установленная версия с номером версии в формате **2.x.-альфа,** удалите эту версию.

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. Отменить регистрацию репозитория «что если», используемого для установки предварительного просмотра.

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

Вы готовы использовать что-если.

## <a name="see-results"></a>Просмотр результатов

В PowerShell выход включает в себя цветные результаты, которые помогут вам увидеть различные типы изменений.

![Развертывание шаблона менеджера ресурсов — что-если операция полная нагрузка ресурсов и типы изменений](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Вывод текста:

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

## <a name="what-if-commands"></a>Команды «что-если»

Для операции «что-если» можно использовать aPI Azure PowerShell или Azure REST.

### <a name="azure-powershell"></a>Azure PowerShell

Чтобы просмотреть предварительный просмотр изменений перед `-Whatif` развертыванием шаблона, добавьте параметр коммутатора в команду развертывания.

* `New-AzResourceGroupDeployment -Whatif`для развертывания групп ресурсов
* `New-AzSubscriptionDeployment -Whatif`и `New-AzDeployment -Whatif` для развертывания уровня подписки

Или можно использовать `-Confirm` параметр коммутатора для предварительного просмотра изменений и получения предложения продолжить развертывание.

* `New-AzResourceGroupDeployment -Confirm`для развертывания групп ресурсов
* `New-AzSubscriptionDeployment -Confirm`и `New-AzDeployment -Confirm` для развертывания уровня подписки

Предыдущие команды возвращают текстовое резюме, которое можно проверить вручную. Чтобы получить объект, который можно программно проверить на предмет изменений, используйте:

* `$results = Get-AzResourceGroupDeploymentWhatIfResult`для развертывания групп ресурсов
* `$results = Get-AzSubscriptionDeploymentWhatIfResult`или `$results = Get-AzDeploymentWhatIfResult` для развертывания уровня подписки

### <a name="azure-rest-api"></a>REST API Azure

Для REST API используйте:

* [Развертывания - Что делать, если](/rest/api/resources/deployments/whatif) для развертывания группы ресурсов
* [Развертывания - Что делать, если при подписке Область](/rest/api/resources/deployments/whatifatsubscriptionscope) для развертывания уровня подписки

## <a name="change-types"></a>Типы изменений

В операции «что,если» перечислены шесть различных типов изменений:

- **Создать**: Ресурс в настоящее время не существует, но определяется в шаблоне. Ресурс будет создан.

- **Удаление**: Этот тип изменения применяется только при использовании [полного режима](deployment-modes.md) для развертывания. Ресурс существует, но не определен в шаблоне. В полном режиме ресурс будет удален. В этот тип изменений включены только ресурсы, [поддерживающие полное удаление режима.](complete-mode-deletion.md)

- **Игнорировать**: Ресурс существует, но не определен в шаблоне. Ресурс не будет развернут или изменен.

- **NoChange**: Ресурс существует и определяется в шаблоне. Ресурс будет передислоцирован, но свойства ресурса не изменятся. Этот тип изменения возвращается, когда `FullResourcePayloads` [ResultFormat](#result-format) установлен на, который является значением по умолчанию.

- **Изменение**: Ресурс существует и определяется в шаблоне. Ресурс будет передислоцирован, а свойства ресурса изменятся. Этот тип изменения возвращается, когда `FullResourcePayloads` [ResultFormat](#result-format) установлен на, который является значением по умолчанию.

- **Развертывание**: Ресурс существует и определяется в шаблоне. Ресурс будет передислоцирован. Свойства ресурса могут или не могут меняться. Операция возвращает этот тип изменения, когда у него нет достаточной информации, чтобы определить, изменятся ли свойства. Вы видите это условие только `ResourceIdOnly`тогда, когда [ResultFormat](#result-format) настроен на .

## <a name="result-format"></a>Формат результата

Вы можете контролировать уровень детали, который возвращается о прогнозируемых изменениях. В командах развертывания`New-Az*Deployment`(), используйте параметр **-WhatIfResultFormat.** В программных командах`Get-Az*DeploymentWhatIf`объекта (), используйте параметр **ResultFormat.**

Установите параметр формата для **FullResourcePayloads,** чтобы получить список ресурсов, которые изменятся, и подробную информацию о свойствах, которые изменятся. Установите параметр формата **на ResourceIdТолько,** чтобы получить список ресурсов, которые будут меняться. Значение по умолчанию — **FullResourcePayloads.**  

Следующие результаты показывают два различных формата вывода:

- Полная полезная нагрузка ресурсов

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

- Идентификатор только ресурса

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Выполнить операцию «что,если»

### <a name="set-up-environment"></a>Настройка среды

Чтобы увидеть, как что-если работает, давайте запустим некоторые тесты. Во-первых, развернуть [шаблон, который создает виртуальную сеть.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json) Вы будете использовать эту виртуальную сеть для проверки того, как изменения сообщаются как что-если.

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>Тестовая модификация

После завершения развертывания вы будете готовы протестировать операцию «что если». На этот раз развернуть [шаблон, который изменяет виртуальную сеть.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json) Не хватает одного исходного тега, подсеть была удалена, и адресная префикс изменилась.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Выход what-if кажется похожим на:

![Развертывание шаблона управления ресурсами what-if вывода операции](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Вывод текста:

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

Обратите внимание в верхней части вывода, что цвета определяются для указания типа изменений.

В нижней части вывода отображается удаление тега Владелец. Префикс адреса изменен с 10.0.0.0/16 до 10.0.0.0.15. Подсеть подназвание поднет001 была удалена. Помните, что эти изменения на самом деле не были развернуты. При развертывании шаблона просматриваются предварительные изменения, которые произойдут.

Некоторые свойства, перечисленные как удаленные, фактически не изменятся. Свойства могут быть неправильно сообщены как удаленные, когда они не находятся в шаблоне, но автоматически устанавливаются во время развертывания в качестве значений по умолчанию. Этот результат считается "шум" в what-if ответ. Окончательный развернутый ресурс будет иметь значения, установленные для свойств. По мере созревания операции «что,если» эти свойства будут отфильтроваться из результата.

## <a name="programmatically-evaluate-what-if-results"></a>Программно оценивать результаты «если»

Теперь давайте программно оценим результаты «что, если» параметрию команды.

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Вы можете увидеть резюме каждого изменения.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>Подтверждение удаления

Операция what-if поддерживается с помощью [режима развертывания.](deployment-modes.md) При установке в полный режим ресурсы, не напаваемые в шаблоне, удаляются. В следующем примере развертывается [шаблон, в который нет ресурсов, определенных](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) в полном режиме.

Для предварительного просмотра изменений перед `-Confirm` развертыванием шаблона используйте параметр коммутатора с командой развертывания. Если изменения выполнены в том виде, в каком вы ожидали, подтвердите, что необходимо завершить развертывание.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Поскольку в шаблоне нет ресурсов и режим развертывания не завершен, виртуальная сеть будет удалена.

![Развертывание шаблона диспетчера ресурсов развертывание режима вывода вывода операции «если операция» завершено](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Вывод текста:

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

Вы видите ожидаемые изменения и можете подтвердить, что вы хотите, чтобы развертывание было запущено.

## <a name="next-steps"></a>Следующие шаги

- Если вы заметили неправильные результаты из предварительного релиза что-если, пожалуйста, сообщите о проблемах на [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- Для развертывания шаблонов с Azure PowerShell см. [Ресурсы развертывание с шаблонами ARM и Azure PowerShell.](deploy-powershell.md)
- Для развертывания шаблонов с REST см. [Ресурсы развертывание с шаблонами ARM и API управления ресурсами REST.](deploy-rest.md)
