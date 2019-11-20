---
title: Развертывание брандмауэра Azure с Зоны доступности помощью PowerShell
description: Из этой статьи вы узнаете, как развернуть брандмауэр Azure с Зоны доступности помощью Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 33dcebf14f4d534962783a30ec94f7ff6529ae0d
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195922"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Развертывание брандмауэра Azure с Зоны доступности помощью Azure PowerShell

Брандмауэр Azure можно настроить во время развертывания. Это позволит охватить несколько зон доступности, что повысит уровень доступности.

Эта функция позволяет выполнять следующие сценарии.

- Вы можете повысить доступность до 99,99% времени работы. Подробнее этот вопрос рассматривается в [Соглашении об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) для Брандмауэра Azure. Если выбраны две или более зон доступности, предоставляется Соглашение об уровне обслуживания на уровне 99,99 %.
- Также Брандмауэр Azure можно связать с определенной ближайшей зоной, используя для этого стандарт обслуживания на уровне 99,95 %.

Дополнительные сведения о Зоны доступности брандмауэре Azure см. в статье [что такое брандмауэр Azure?](overview.md)

В следующем Azure PowerShell примере показано, как можно развернуть брандмауэр Azure с Зоны доступности.

## <a name="create-a-firewall-with-availability-zones"></a>Создание брандмауэра с Зоны доступности

В этом примере создается брандмауэр в зонах 1, 2 и 3.

При создании стандартного общедоступного IP-адреса зона не указывается. При этом по умолчанию создается избыточный по зоне IP-адрес. Стандартные общедоступные IP-адреса можно настроить либо во всех зонах, либо в одной зоне.

Важно помнить, что у вас нет брандмауэра в зоне 1 и IP-адреса в зоне 2. Но вы можете использовать брандмауэр в зоне 1 и IP-адрес во всех зонах, а также брандмауэр и IP-адрес в одной зоне для поиска с учетом расположения.

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

## <a name="next-steps"></a>Дополнительная информация

- [Руководство по мониторингу журналов Брандмауэра Azure](./tutorial-diagnostics.md)
