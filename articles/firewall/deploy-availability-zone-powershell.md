---
title: Развертывание брандмауэра Azure с зонами доступности с помощью Azure PowerShell
description: В этой статье вы узнаете, как развернуть брандмауэр Azure с зонами доступности с помощью Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/20/2019
ms.author: victorh
ms.openlocfilehash: 2fa6a62a28a1536da83994cb07b7c5fa5d7bda9f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276916"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Развертывание Azure брандмауэр подключения к с зонами доступности с помощью Azure PowerShell

> [!IMPORTANT]
> Брандмауэр Azure с зонами доступности в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Брандмауэр Azure можно настроить во время развертывания для охвата нескольких зонах доступности для повышения уровня доступности.

Эта функция поддерживает следующие сценарии:

- Вы можете увеличить доступность 99,99%. Дополнительные сведения см. в разделе брандмауэр Azure [уровень соглашения об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). 99,99% время работы соглашение об уровне ОБСЛУЖИВАНИЯ предоставляется, если выбраны два или несколько зон доступности.
- Брандмауэр Azure можно также связать с определенной зоной только в целях с учетом расположения с помощью службы стандартный 99,95% соглашение об уровне ОБСЛУЖИВАНИЯ.

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

## <a name="next-steps"></a>Дальнейшие действия

- [Руководство. Мониторинг журналов и метрик Брандмауэра Azure](./tutorial-diagnostics.md)
