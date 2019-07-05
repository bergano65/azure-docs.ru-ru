---
title: Развертывание брандмауэра Azure с помощью нескольких общедоступных IP-адресов, с помощью Azure PowerShell
description: В этой статье вы узнаете, как развернуть брандмауэр подключения к Azure с помощью нескольких общедоступных IP-адресов, с помощью Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/2/2019
ms.author: victorh
ms.openlocfilehash: a5a53766df3338bb36913b589ebda970de55ec94
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491938"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Развернуть брандмауэр подключения к Azure с помощью нескольких общедоступных IP-адресов, с помощью Azure PowerShell

> [!IMPORTANT]
> Брандмауэр Azure с помощью нескольких общедоступных IP-адресов в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Вы можете развернуть брандмауэр подключения к Azure с помощью до 100 общедоступные IP-адреса.

Эта функция поддерживает следующие сценарии:

- **DNAT** -можно преобразовать несколько экземпляров стандартный порт для внутренних серверов. Например если у вас есть два общедоступных IP-адресов, можно легко преобразовать TCP-порт 3389 (RDP) для обоих IP-адресов.
- **SNAT** -доступны дополнительные порты для исходящих подключений SNAT, снижая вероятность нехватки портов SNAT. В настоящее время брандмауэр Azure случайным образом выбирает источник общедоступный IP-адрес для подключения. Если у вас есть какой-либо подчиненные фильтрации в сети, необходимо разрешить все общедоступные IP-адреса, связанные с брандмауэром.

Приведенные ниже примеры Azure PowerShell показано, как можно добавлять, удалять и настроить общедоступный IP-адреса для брандмауэра Azure.

> [!NOTE]
> В общедоступной предварительной версии Если при добавлении или удалении общедоступный IP-адрес, работающей брандмауэру, существующие входящие подключения, с помощью правил DNAT может работать на 40-120 секунд. Невозможно удалить первый общедоступный IP-адрес, назначенные брандмауэр, если брандмауэр является освобождены или удалены.

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Добавьте общедоступный IP-адрес в существующий брандмауэр

В этом примере общедоступный IP-адрес *azFwPublicIp1* как подключенные к брандмауэру.

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

В этом примере общедоступный IP-адрес *azFwPublicIp1* отсоединенными от брандмауэра.

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

## <a name="next-steps"></a>Дальнейшие действия

* [Учебник. Мониторинг журналов и метрик Брандмауэра Azure](./tutorial-diagnostics.md)
