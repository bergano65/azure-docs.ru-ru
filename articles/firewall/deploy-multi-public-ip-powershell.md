---
title: Развертывание брандмауэра Azure с несколькими общедоступными IP-адресами с помощью PowerShell
description: В этой статье вы узнаете, как развернуть брандмауэр Azure с несколькими общедоступными IP-адресами с помощью Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 21f645e64c9944ed102f538710ea6facc26c7e83
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314030"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Развертывание Брандмауэра Azure с использованием нескольких общедоступных IP-адресов с помощью Azure PowerShell

Эта функция позволяет следующие сценарии:

- **DNAT**. Позволяет преобразовать несколько стандартных экземпляров порта для внутренних серверов. Например, если существует два общедоступных IP-адреса, то для обоих IP-адреса можно преобразовать TCP-порт 3389 (RDP).
- **SNAT**. Дополнительные порты доступны для исходящих SNAT подключений, что снижает вероятность нехватки SNAT портов. На данный момент Брандмауэр Azure случайно выбирает общедоступные IP-адреса источника, которые можно использовать для подключения. Если в сети установлена любая фильтрация, необходимо разрешить все публичные IP-адреса, которые связанные с брандмауэром.
 
Портал Azure, Azure PowerShell, Azure CLI, REST и шаблоны позволяют использовать брандмауэр Azure с несколькими общедоступными IP-адресами. Можно развернуть брандмауэр Azure с до 100 общедоступными IP-адресами.

Следующие примеры Azure PowerShell показывают, как можно настроить, добавить и удалить общедоступные IP-адреса для Azure Firewall.

> [!NOTE]
> Вы не можете удалить первую ipConfiguration со страницы конфигурации IP-адреса общедоступного IP-адреса Azure Firewall. Если вы хотите изменить IP-адрес, вы можете использовать Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Создание брандмауэра с двумя или более общедоступными IP-адресами

Этот пример создает брандмауэр, прикрепленный к виртуальной сети *Vnet* с двумя общедоступными IP-адресами.

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

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Добавление общедоступного IP-адреса в существующий брандмауэр

В этом примере к брандмауэру прикрепляется общедоступный IP-адрес *azFwPublicIp1.*

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Удалить общедоступный IP-адрес из существующего брандмауэра

В этом примере общедоступный IP-адрес *azFwPublicIp1* отделен от брандмауэра.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Следующие шаги

* [Быстрый запуск: Создание брандмауэра Azure с несколькими общедоступными IP-адресами - шаблон менеджера ресурсов](quick-create-multiple-ip-template.md)
