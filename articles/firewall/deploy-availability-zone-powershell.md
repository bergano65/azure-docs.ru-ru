---
title: Развертывание брандмауэра Azure с зонами доступности с помощью Azure PowerShell
description: В этой статье вы узнаете, как развернуть брандмауэр Azure с зонами доступности с помощью Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: 56958eedceeb4602589d65d5e0eb7b10e8a9ff2d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704002"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Развертывание Azure брандмауэр подключения к с зонами доступности с помощью Azure PowerShell

Брандмауэр Azure можно настроить во время развертывания. Это позволит охватить несколько зон доступности, что повысит уровень доступности.

Эта функция поддерживает следующие сценарии:

- Вы можете увеличить доступность 99,99%. Подробнее этот вопрос рассматривается в [Соглашении об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) для Брандмауэра Azure. Если выбраны две или более зон доступности, предоставляется Соглашение об уровне обслуживания на уровне 99,99 %.
- Также Брандмауэр Azure можно связать с определенной ближайшей зоной, используя для этого стандарт обслуживания на уровне 99,95 %.

Дополнительные сведения о зонах доступности брандмауэра Azure, см. в разделе [что такое брандмауэр Azure?](overview.md)

В следующем примере Azure PowerShell показано, как развернуть брандмауэр подключения к Azure с зонами доступности.

## <a name="create-a-firewall-with-availability-zones"></a>Создание брандмауэра с зонами доступности

В этом примере создает брандмауэр в зонах 1, 2 и 3.

При создании стандартный общедоступный IP-адрес определенного зона не указана. Это создает избыточные в пределах зоны IP-адрес по умолчанию. Стандартные общедоступные IP-адреса можно настроить в все зоны или в одной зоне.

Важно знать, так как не может иметь брандмауэром в зоне 1 и IP-адреса в зоне 2. Но может быть брандмауэр в зоне 1 и IP-адрес во всех зонах или брандмауэром и IP-адресом в той же зоне единый целях с учетом расположения.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1)
  -Zone 1,2,3
```

## <a name="next-steps"></a>Следующие шаги

- [Учебник. Мониторинг журналов и метрик Брандмауэра Azure](./tutorial-diagnostics.md)
