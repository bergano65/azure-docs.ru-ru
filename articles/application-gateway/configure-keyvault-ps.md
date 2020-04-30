---
title: Настройка завершения TLS с помощью сертификатов Key Vault — PowerShell
titleSuffix: Azure Application Gateway
description: Узнайте, как интегрировать шлюз приложений Azure с Key Vault для сертификатов сервера, подключенных к прослушивателям с поддержкой HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 02/27/2020
ms.author: victorh
ms.openlocfilehash: ffda4b41497a9fd84db5fcee36202eb1c1dca2c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457847"
---
# <a name="configure-tls-termination-with-key-vault-certificates-by-using-azure-powershell"></a>Настройка завершения TLS с использованием Key Vault сертификатов с помощью Azure PowerShell

[Azure Key Vault](../key-vault/general/overview.md) — это хранилище секретов, управляемое платформой, которое можно использовать для защиты секретов, ключей и сертификатов TLS/SSL. Шлюз приложений Azure поддерживает интеграцию с Key Vault для сертификатов сервера, подключенных к прослушивателям с поддержкой HTTPS. Эта поддержка ограничена SKU шлюза приложений версии 2.

Дополнительные сведения см. [в статье завершение TLS с использованием сертификатов Key Vault](key-vault-certs.md).

В этой статье показано, как использовать скрипт Azure PowerShell для интеграции хранилища ключей с шлюзом приложений для сертификатов завершения TLS/SSL.

Для работы с этой статьей требуется Azure PowerShell Module версии 1.0.0 или более поздней. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Чтобы выполнить команды в этой статье, необходимо также создать подключение к Azure, выполнив `Connect-AzAccount`команду.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Перед началом необходимо установить модуль Манажедсервицеидентити:

```azurepowershell
Install-Module -Name Az.ManagedServiceIdentity
Connect-AzAccount
Select-AzSubscription -Subscription <your subscription>
```

## <a name="example-script"></a>Пример сценария

### <a name="set-up-variables"></a>Настройка переменных

```azurepowershell
$rgname = "KeyVaultTest"
$location = "East US"
$kv = "TestKeyVaultAppGw"
$appgwName = "AppGwKVIntegration"
```

### <a name="create-a-resource-group-and-a-user-managed-identity"></a>Создание группы ресурсов и удостоверения, управляемого пользователем

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
$identity = New-AzUserAssignedIdentity -Name "appgwKeyVaultIdentity" `
  -Location $location -ResourceGroupName $rgname
```

### <a name="create-a-key-vault-policy-and-certificate-to-be-used-by-the-application-gateway"></a>Создание хранилища ключей, политики и сертификата для использования шлюзом приложений

```azurepowershell
$keyVault = New-AzKeyVault -Name $kv -ResourceGroupName $rgname -Location $location -EnableSoftDelete 
Set-AzKeyVaultAccessPolicy -VaultName $kv -PermissionsToSecrets get -ObjectId $identity.PrincipalId

$policy = New-AzKeyVaultCertificatePolicy -ValidityInMonths 12 `
  -SubjectName "CN=www.contoso11.com" -IssuerName self `
  -RenewAtNumberOfDaysBeforeExpiry 30
Set-AzKeyVaultAccessPolicy -VaultName $kv -EmailAddress <your email address> -PermissionsToCertificates create,get,list
$certificate = Add-AzKeyVaultCertificate -VaultName $kv -Name "cert1" -CertificatePolicy $policy
$certificate = Get-AzKeyVaultCertificate -VaultName $kv -Name "cert1"
$secretId = $certificate.SecretId.Replace($certificate.Version, "")
```
> [!NOTE]
> Для правильной работы функции завершения TLS необходимо использовать флаг-Енаблесофтделете. Если вы настраиваете [Key Vault обратимое удаление с помощью портала](../key-vault/general/overview-soft-delete.md#soft-delete-behavior), срок хранения должен храниться в 90 дней, то есть в значении по умолчанию. Шлюз приложений еще не поддерживает другой срок хранения. 

### <a name="create-a-virtual-network"></a>Создание виртуальной сети

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-virtual-ip-vip-address"></a>Создание статического общедоступного виртуального IP-адреса (VIP)

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-pool-and-front-end-ports"></a>Создание пулов и интерфейсных портов

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "port2" -Port 80
```

### <a name="point-the-tlsssl-certificate-to-your-key-vault"></a>Наведите сертификат TLS/SSL в хранилище ключей.

```azurepowershell
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert1" -KeyVaultSecretId $secretId
```

### <a name="create-listeners-rules-and-autoscale"></a>Создание прослушивателей, правил и автомасштабирования

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Https `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "listener2" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02
$poolSetting01 = New-AzApplicationGatewayBackendHttpSetting -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool
$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
```

### <a name="assign-the-user-managed-identity-to-the-application-gateway"></a>Назначение управляемого пользователем удостоверения шлюзу приложений

```azurepowershell
$appgwIdentity = New-AzApplicationGatewayIdentity -UserAssignedIdentityId $identity.Id
```

### <a name="create-the-application-gateway"></a>Создание шлюза приложений

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -Identity $appgwIdentity -ResourceGroupName $rgname `
  -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 `
  -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
  -FrontendPorts @($fp01, $fp02) -HttpListeners @($listener01, $listener02) `
  -RequestRoutingRules @($rule01, $rule02) -Sku $sku `
  -SslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="next-steps"></a>Дальнейшие шаги

[Дополнительные сведения об увольнении TLS](ssl-overview.md)
