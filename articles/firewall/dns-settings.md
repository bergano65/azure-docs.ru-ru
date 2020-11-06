---
title: Параметры DNS брандмауэра Azure (Предварительная версия)
description: Вы можете настроить брандмауэр Azure с помощью DNS-сервера и параметров прокси-сервера DNS.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: efe608437f350a29147cf10989cdb6c17a23196d
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398000"
---
# <a name="azure-firewall-dns-settings-preview"></a>Параметры DNS брандмауэра Azure (Предварительная версия)

> [!IMPORTANT]
> Параметры DNS брандмауэра Azure в настоящее время находятся в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Вы можете настроить пользовательский DNS-сервер и включить DNS-прокси для брандмауэра Azure. Эти параметры можно настроить при развертывании брандмауэра или более поздней версии с помощью страницы **параметров DNS** .

## <a name="dns-servers"></a>"Серверы DNS"

DNS-сервер поддерживает и разрешает доменные имена IP-адресам. По умолчанию брандмауэр Azure использует Azure DNS для разрешения имен. Параметр **DNS-сервера** позволяет настроить собственные DNS-серверы для разрешения имен брандмауэра Azure. Можно настроить один или несколько серверов.

> [!NOTE]
> Для брандмауэров Azure, управляемых с помощью диспетчера брандмауэра Azure, параметры DNS настраиваются в соответствующей политике брандмауэра Azure.

### <a name="configure-custom-dns-servers-preview---azure-portal"></a>Настройка пользовательских DNS-серверов (Предварительная версия) — портал Azure

1. В разделе **Параметры** брандмауэра Azure выберите **параметры DNS**.
2. В разделе **DNS-серверы** можно ввести или добавить существующие DNS-серверы, которые ранее были указаны в виртуальной сети.
3. Нажмите кнопку **Сохранить**.
4. Теперь брандмауэр направляет трафик DNS на указанные DNS-серверы для разрешения имен.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="DNS-серверы":::

### <a name="configure-custom-dns-servers-preview---azure-cli"></a>Настройка пользовательских DNS-серверов (Предварительная версия) — Azure CLI

В следующем примере брандмауэр Azure обновляется с помощью пользовательских DNS-серверов, использующих Azure CLI.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> Для команды `az network firewall` требуется установить расширение Azure CLI `azure-firewall` . Его можно установить с помощью команды `az extension add --name azure-firewall` . 

### <a name="configure-custom-dns-servers-preview---azure-powershell"></a>Настройка пользовательских DNS-серверов (Предварительная версия) — Azure PowerShell

В следующем примере брандмауэр Azure обновляется с помощью пользовательских DNS-серверов, использующих Azure PowerShell.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy-preview"></a>DNS-прокси (Предварительная версия)

Вы можете настроить брандмауэр Azure так, чтобы он действовал в качестве DNS-прокси. DNS-прокси выступает в качестве посредника для запросов DNS от клиентских виртуальных машин к DNS-серверу. Если настроен настраиваемый DNS-сервер, следует включить DNS-прокси, чтобы избежать несоответствия разрешений DNS и включить фильтрацию FQDN в правилах сети.

Если не включить DNS-прокси, запросы DNS от клиента могут передаваться на DNS-сервер в другое время или возвращать другой ответ, по сравнению с брандмауэром. DNS-прокси помещает брандмауэр Azure в путь клиентских запросов, чтобы избежать несогласованности.

Конфигурация прокси-сервера DNS требует трех шагов:
1. Включите DNS-прокси в параметрах DNS брандмауэра Azure.
2. При необходимости настройте пользовательский DNS-сервер или используйте предоставленное значение по умолчанию.
3. Наконец, необходимо настроить частный IP-адрес брандмауэра Azure в качестве пользовательского DNS-адреса в параметрах DNS-сервера виртуальной сети. Это гарантирует, что трафик DNS будет перенаправлен в брандмауэр Azure.

### <a name="configure-dns-proxy-preview---azure-portal"></a>Настройка прокси-сервера DNS (Предварительная версия) — портал Azure

Чтобы настроить DNS-прокси, необходимо настроить параметр DNS-серверов виртуальной сети для использования частного IP-адреса брандмауэра. Затем включите DNS-прокси в **параметрах DNS** брандмауэра Azure.

#### <a name="configure-virtual-network-dns-servers"></a>Настройка DNS-серверов виртуальной сети 

1. Выберите виртуальную сеть, в которой трафик DNS будет маршрутизироваться через брандмауэр Azure.
2. В разделе **Параметры** выберите **DNS-серверы**.
3. В разделе **DNS-серверы** выберите **Настраиваемый** .
4. Введите частный IP-адрес брандмауэра.
5. Нажмите кнопку **Сохранить**.
6. Перезапустите виртуальные машины, подключенные к виртуальной сети, чтобы назначить им новые параметры DNS-сервера. Виртуальные машины продолжают использовать свои текущие параметры DNS, пока они не будут перезагружены.

#### <a name="enable-dns-proxy-preview"></a>Включение DNS-прокси (Предварительная версия)

1. Выберите свой брандмауэр Azure.
2. В разделе **Параметры** выберите **параметры DNS**.
3. По умолчанию **DNS-прокси** отключен. Если этот параметр включен, брандмауэр прослушивает порт 53 и перенаправляет запросы DNS на настроенные DNS-серверы.
4. Проверьте конфигурацию **DNS-серверов** и убедитесь, что параметры соответствуют вашей среде.
5. Нажмите кнопку **Сохранить**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="DNS-прокси":::

### <a name="configure-dns-proxy-preview---azure-cli"></a>Настройка прокси-сервера DNS (Предварительная версия) — Azure CLI

Настройка параметров прокси-сервера DNS в брандмауэре Azure и обновление виртуальных сетей для использования брандмауэра Azure в качестве DNS-сервера можно выполнить с помощью Azure CLI.

#### <a name="configure-virtual-network-dns-servers"></a>Настройка DNS-серверов виртуальной сети

В этом примере виртуальная сеть настраивается на использование брандмауэра Azure в качестве DNS-сервера.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

#### <a name="enable-dns-proxy-preview"></a>Включение DNS-прокси (Предварительная версия)

В этом примере включается функция прокси-сервера DNS в брандмауэре Azure.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

### <a name="configure-dns-proxy-preview---azure-powershell"></a>Настройка прокси-сервера DNS (Предварительная версия) — Azure PowerShell

Настройка параметров прокси-сервера DNS и обновление виртуальных сетей для использования брандмауэра Azure в качестве DNS-сервера можно выполнить с помощью Azure PowerShell.

#### <a name="configure-virtual-network-dns-servers"></a>Настройка DNS-серверов виртуальной сети

 В этом примере виртуальная сеть настраивается на использование брандмауэра Azure в качестве DNS-сервера.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

#### <a name="enable-dns-proxy-preview"></a>Включение DNS-прокси (Предварительная версия)

В этом примере включается функция прокси-сервера DNS в брандмауэре Azure.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Дальнейшие шаги

[Фильтрация FQDN в правилах сети](fqdn-filtering-network-rules.md)
