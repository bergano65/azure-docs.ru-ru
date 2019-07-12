---
title: Развертывание брандмауэра Azure с помощью нескольких общедоступных IP-адресов, с помощью Azure PowerShell
description: В этой статье вы узнаете, как развернуть брандмауэр подключения к Azure с помощью нескольких общедоступных IP-адресов, с помощью Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: ce47612f18ee64caa3a053001deb5448f7c27bfd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703984"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Развернуть брандмауэр подключения к Azure с помощью нескольких общедоступных IP-адресов, с помощью Azure PowerShell

> [!IMPORTANT]
> Брандмауэр Azure с помощью нескольких общедоступных IP-адресов доступен через Azure PowerShell, Azure CLI, REST и шаблоны. Пользовательском интерфейсе портала постепенно добавляемый регионы и будут доступны во всех регионах после завершения развертывания.

Вы можете развернуть брандмауэр подключения к Azure с помощью до 100 общедоступные IP-адреса.

Эта функция поддерживает следующие сценарии:

- **DNAT**. Позволяет преобразовать несколько стандартных экземпляров порта для внутренних серверов. Например, если существует два общедоступных IP-адреса, то для обоих IP-адреса можно преобразовать TCP-порт 3389 (RDP).
- **SNAT**. Дополнительные порты доступны для исходящих SNAT подключений, что снижает вероятность нехватки SNAT портов. На данный момент Брандмауэр Azure случайно выбирает общедоступные IP-адреса источника, которые можно использовать для подключения. Если в сети установлена любая фильтрация, необходимо разрешить все публичные IP-адреса, которые связанные с брандмауэром.

Приведенные ниже примеры Azure PowerShell показано, как можно настроить, добавление и удаление общедоступных IP-адресов для брандмауэра Azure.

> [!NOTE]
> Нельзя удалить первый IP-конфигурации на странице конфигурации общедоступный адрес для IP-адрес брандмауэра Azure. Если вы хотите изменить IP-адрес, можно использовать Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Создание брандмауэра с помощью двух или более общих IP-адресов

В этом примере создает брандмауэр, подключенный к виртуальной сети *виртуальной сети* с два общедоступных IP-адресов.

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

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Добавьте общедоступный IP-адрес в существующий брандмауэр

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

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Удалить общедоступный IP-адрес из существующий брандмауэр

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
