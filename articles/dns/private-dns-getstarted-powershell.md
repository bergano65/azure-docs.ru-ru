---
title: Краткое руководство. Создание частной зоны Azure DNS с помощью Azure PowerShell
description: В этой статье описано, как создать и проверить частную зону и запись DNS в Azure DNS. Это пошаговое руководство по созданию первой частной зоны DNS и записи DNS, а также управлению ими с помощью PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: cf9ca1070461effc69d67614a11b1abd05363310
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162126"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-azure-powershell"></a>Краткое руководство. Создание частной зоны Azure DNS с помощью Azure PowerShell

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Эта статья поможет вам создать свою первую частную зону и первую запись DNS с помощью Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Зона DNS используется для размещения DNS-записей определенного домена. Чтобы разместить свой домен в Azure DNS, необходимо создать зону DNS для этого доменного имени. Каждая запись DNS для вашего домена создается внутри этой зоны DNS. Чтобы опубликовать частную зону DNS в виртуальной сети, укажите список виртуальных сетей, которые могут разрешать записи в зоне.  Они называются *связанными* виртуальными сетями. Когда автоматическая регистрация включена, Azure DNS также обновляет записи зоны при каждом создании виртуальной машины, изменении ее IP-адреса или удалении.

В этой статье раскрываются следующие темы:

> [!div class="checklist"]
> * Создание частной зоны DNS.
> * Создание тестовых виртуальных машин
> * Создание дополнительной записи DNS
> * Проверка частной зоны

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

При необходимости эти инструкции можно выполнить с помощью [Azure CLI](private-dns-getstarted-cli.md).

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Сначала нужно создать группу ресурсов, которая будет включать зону DNS. 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-private-dns-zone"></a>Создание частной зоны DNS

Зона DNS создается с помощью командлета `New-AzPrivateDnsZone`.

В следующем примере создается виртуальная сеть с именем **myAzureVNet**. Затем создается зона DNS с именем **private.contoso.com** в группе ресурсов **MyAzureResourceGroup**, которая связывает зону DNS с виртуальной сетью **MyAzureVnet** и включает автоматическую регистрацию.

```azurepowershell
Install-Module -Name Az.PrivateDns -force

$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

$zone = New-AzPrivateDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup

$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName private.contoso.com `
  -ResourceGroupName MyAzureResourceGroup -Name "mylink" `
  -VirtualNetworkId $vnet.id -EnableRegistration
```

Если вы хотите создать зону только для разрешения имен (без автоматической регистрации имени узла), параметр `-EnableRegistration` можно опустить.

### <a name="list-dns-private-zones"></a>Список частных зон DNS

Если опустить имя зоны в командлете `Get-AzPrivateDnsZone`, можно получить список всех зон в группе ресурсов. Эта операция возвращает массив объектов зоны.

```azurepowershell
$zones = Get-AzPrivateDnsZone -ResourceGroupName MyAzureResourceGroup
$zones
```

Если опустить имя зоны и имя группы ресурсов в командлете `Get-AzPrivateDnsZone`, то можно получить список всех зон в подписке Azure.

```azurepowershell
$zones = Get-AzPrivateDnsZone
$zones
```

## <a name="create-the-test-virtual-machines"></a>Создание тестовых виртуальных машин

Теперь создайте две виртуальные машины, чтобы иметь возможность проверить частную зону DNS.

```azurepowershell
New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

Ее выполнение может занять несколько минут.

## <a name="create-an-additional-dns-record"></a>Создание дополнительной записи DNS

Для создания наборов записей используется командлет `New-AzPrivateDnsRecordSet`. В следующем примере создается запись с относительным именем **db** в зоне DNS **private.contoso.com** в группе ресурсов **MyAzureResourceGroup**. Полное доменное имя набора записей — **db.private.contoso.com**. Тип записи — "A", IP-адрес — "10.2.0.4", а срок жизни составляет 3600 секунд.

```azurepowershell
New-AzPrivateDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Просмотр записей DNS

Чтобы просмотреть список записей DNS в зоне, используйте следующую команду:

```azurepowershell
Get-AzPrivateDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="test-the-private-zone"></a>Проверка частной зоны

Теперь вы можете проверить разрешение имени для частной зоны **private.contoso.com**.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Настройка виртуальных машин для получения входящего ICMP-трафика

Можно воспользоваться командой проверки связи (ping) для проверки разрешения имени. Для этого настройте брандмауэр для обеих виртуальных машин, чтобы получать входящие пакеты ICMP.

1. Подключитесь к myVM01 и откройте окно Windows PowerShell с правами администратора.
2. Выполните следующую команду:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Повторите действия для myVM02.

### <a name="ping-the-vms-by-name"></a>Проверка связи с виртуальными машинами по имени

1. В командной строке Windows PowerShell на виртуальной машине myVM02 проверьте связь с myVM01, используя автоматически зарегистрированное имя узла.

   ```
   ping myVM01.private.contoso.com
   ```

   Результат должен выглядеть примерно так:

   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. Теперь проверьте связь с именем **db**, созданным ранее.

   ```
   ping db.private.contoso.com
   ```

   Результат должен выглядеть примерно так:

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milliseconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Удаление всех ресурсов

Удалите группу ресурсов **MyAzureResourceGroup**, если ресурсы, созданные в этой статье, больше не нужны.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Сценарии применения частных зон DNS Azure](private-dns-scenarios.md)
