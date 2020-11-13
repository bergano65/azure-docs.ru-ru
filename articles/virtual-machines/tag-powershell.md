---
title: Пометка виртуальной машины с помощью PowerShell
description: Дополнительные сведения о добавлении тегов виртуальной машины с помощью PowerShell
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 1efb512923caed97126bdb4ee6267c6a9b57f251
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595033"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>Пометка виртуальной машины в Azure с помощью PowerShell

В этой статье описывается, как пометить виртуальную машину в Azure с помощью PowerShell. Теги — это определяемые пользователем пары "ключ-значение", которые можно помещать непосредственно в ресурс или группу ресурсов. В настоящее время Azure поддерживает до 50 тегов на ресурс и группу ресурсов. Теги можно добавлять к ресурсу во время его создания или к уже существующему ресурсу. Если вы хотите пометить виртуальную машину с помощью Azure CLI, см. раздел [как разметить виртуальную машину в Azure с помощью Azure CLI](tag-cli.md).


Используйте `Get-AzVM` командлет, чтобы просмотреть текущий список тегов для виртуальной машины.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

Если ваша виртуальная машина уже содержит теги, вы увидите все теги в формате списка.

Чтобы добавить теги, используйте `Set-AzResource` команду. При обновлении тегов с помощью PowerShell Теги обновляются в целом. При добавлении одного тега к ресурсу, который уже содержит теги, необходимо включить все теги, которые нужно разместить в ресурсе. Ниже представлен пример добавления тегов к ресурсу с помощью командлетов PowerShell.

Присвойте переменной все текущие теги для виртуальной машины `$tags` с помощью `Get-AzResource` `Tags` Свойства и.

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

Чтобы просмотреть текущие теги, введите переменную.

```azurepowershell-interactive
$tags
```

Вот как могут выглядеть выходные данные:

```output
Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

В следующем примере мы добавим тег `Location` с именем со значением `myLocation` . Используйте `+=` для добавления новой пары "ключ-значение" в `$tags` список.

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

Используйте `Set-AzResource` , чтобы задать все теги, определенные в переменной *$Tags* на виртуальной машине.

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

Используйте `Get-AzResource` для вывода всех тегов в ресурсе.

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

Результат должен выглядеть примерно следующим образом, который теперь включает новый тег:

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```


**Дальнейшие действия**

- Дополнительные сведения о добавлении тегов для ресурсов Azure см. в статьях [Общие сведения об Azure Resource Manager](../azure-resource-manager/management/overview.md) и [Использование тегов для организации ресурсов в Azure](../azure-resource-manager/management/tag-resources.md).
- Сведения о том, как теги могут помочь в управлении использованием ресурсов Azure, см. в статьях [Расшифровка счета за использование Microsoft Azure](../cost-management-billing/understand/review-individual-bill.md) и [Получение ценных сведений о потреблении ресурсов Microsoft Azure](../cost-management-billing/manage/usage-rate-card-overview.md).
