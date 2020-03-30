---
title: Развертывание брандмауэра Azure с зонами доступности с помощью PowerShell
description: В этой статье вы узнаете, как развернуть брандмауэр Azure с зонами доступности с помощью Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 33dcebf14f4d534962783a30ec94f7ff6529ae0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74195922"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Развертывание Брандмауэра Azure с использованием Зон доступности и Azure PowerShell

Брандмауэр Azure можно настроить во время развертывания. Это позволит охватить несколько зон доступности, что повысит уровень доступности.

Эта функция позволяет следующие сценарии:

- Вы можете увеличить доступность до 99,99% времени простоя. Подробнее этот вопрос рассматривается в [Соглашении об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) для Брандмауэра Azure. Если выбраны две или более зон доступности, предоставляется Соглашение об уровне обслуживания на уровне 99,99 %.
- Также Брандмауэр Azure можно связать с определенной ближайшей зоной, используя для этого стандарт обслуживания на уровне 99,95 %.

Для получения дополнительной информации о зонах доступности брандмауэра Azure [читайте](overview.md) в

Следующий пример Azure PowerShell показывает, как можно развернуть брандмауэр Azure с зонами доступности.

## <a name="create-a-firewall-with-availability-zones"></a>Создание брандмауэра с зонами доступности

Этот пример создает брандмауэр в зонах 1, 2 и 3.

При создании стандартного публичного IP-адреса конкретная зона не указывается. Это создает зонально-излишний IP-адрес по умолчанию. Стандартные общедоступные IP-адреса могут быть настроены либо во всех зонах, либо в одной зоне.

Важно знать, потому что вы не можете иметь брандмауэр в зоне 1 и IP-адрес в зоне 2. Но вы можете иметь брандмауэр в зоне 1 и IP-адрес во всех зонах, или брандмауэр и IP-адрес в одной и той же зоне для целей близости.

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

## <a name="next-steps"></a>Дальнейшие действия

- [Руководство. Журналы мониторинга брандмауэра Azure](./tutorial-diagnostics.md)
