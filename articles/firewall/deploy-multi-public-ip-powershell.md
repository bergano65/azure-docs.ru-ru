---
title: Развертывание брандмауэра Azure с несколькими общедоступными IP-адресами с помощью Azure PowerShell
description: Из этой статьи вы узнаете, как развернуть брандмауэр Azure с несколькими общедоступными IP-адресами с помощью Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: ba2736ae69d0bf7feff5f852da2446bfa7a722a6
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325232"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Развертывание брандмауэра Azure с несколькими общедоступными IP-адресами с помощью Azure PowerShell

Эта функция позволяет выполнять следующие сценарии.

- **DNAT**. Позволяет преобразовать несколько стандартных экземпляров порта для внутренних серверов. Например, если существует два общедоступных IP-адреса, то для обоих IP-адреса можно преобразовать TCP-порт 3389 (RDP).
- **SNAT**. Дополнительные порты доступны для исходящих SNAT подключений, что снижает вероятность нехватки SNAT портов. На данный момент Брандмауэр Azure случайно выбирает общедоступные IP-адреса источника, которые можно использовать для подключения. Если в сети установлена любая фильтрация, необходимо разрешить все публичные IP-адреса, которые связанные с брандмауэром.
 
Брандмауэр Azure с несколькими общедоступными IP-адресами можно получить с помощью портал Azure, Azure PowerShell, Azure CLI, RESTFUL и шаблонов. Вы можете развернуть брандмауэр Azure, содержащий до 100 общедоступных IP-адресов.

В следующих примерах Azure PowerShell показано, как можно настроить, добавить и удалить общедоступные IP-адреса для брандмауэра Azure.

> [!NOTE]
> Вы не можете удалить первую конфигурацию с страницы конфигурации общедоступного IP-адреса брандмауэра Azure. Если вы хотите изменить IP-адрес, можно использовать Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Создание брандмауэра с двумя или более общедоступными IP-адресами

В этом примере создается брандмауэр, подключенный к виртуальной сети *виртуальных сетей с* двумя общедоступными IP-адресами.

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

В этом примере общедоступный IP-адрес *azFwPublicIp1* подключен к брандмауэру.

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

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Удаление общедоступного IP-адреса из существующего брандмауэра

В этом примере общедоступный IP-адрес *azFwPublicIp1* отсоединяется от брандмауэра.

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

* [Учебник. Мониторинг журналов и метрик Брандмауэра Azure](./tutorial-diagnostics.md)
