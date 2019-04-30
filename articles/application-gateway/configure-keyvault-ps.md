---
title: Настройка моста SSL с помощью сертификаты Key Vault с помощью Azure PowerShell
description: Узнайте, как интегрировать шлюз приложений Azure с хранилищем ключей для сертификатов сервера, подключенных к прослушиватели протокол HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/22/2019
ms.author: victorh
ms.openlocfilehash: 7c31801156ee321fe93d73de41fc68179835261a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831118"
---
# <a name="configure-ssl-termination-with-key-vault-certificates-using-azure-powershell"></a>Настройка моста SSL с помощью сертификаты Key Vault с помощью Azure PowerShell

[Azure Key Vault](../key-vault/key-vault-whatis.md) — это управляемая платформа секретное хранилище позволяет защитить секретные данные, ключи и сертификаты SSL. Шлюз приложений поддерживает интеграцию с хранилищем ключей (в общедоступной предварительной версии) для сертификатов сервера, подключенных к прослушиватели протокол HTTPS. Эта поддержка ограничена v2 SKU шлюза приложений.

Дополнительные сведения см. в разделе [завершение запросов SSL с сертификатами в Key Vault](key-vault-certs.md).

В этой статье показан сценарий Azure PowerShell для интеграции хранилища ключей с помощью шлюза приложений для завершения SSL-сертификатов.

> [!IMPORTANT]
> Интеграция хранилища ключей шлюза приложения находится в общедоступной предварительной версии. Предварительная версия предоставляется без соглашения об уровне обслуживания. Не рекомендуем использовать ее в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

В этой статье требуется модуль Azure PowerShell версии 1.0.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable Az`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-az-ps). Для выполнения команд в этой статье, необходимо также выполнить `Connect-AzAccount` для создания соединения с Azure.

## <a name="prerequisites"></a>Технические условия

Необходимо установить перед началом модуль ManagedServiceIdentity.

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

### <a name="create-a-resource-group-and-a-user-managed-identity"></a>Создайте группу ресурсов и пользовательского управляемого удостоверения

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
$identity = New-AzUserAssignedIdentity -Name "appgwKeyVaultIdentity" `
  -Location $location -ResourceGroupName $rgname
```

### <a name="create-key-vault-policy-and-certificate-to-be-used-by-application-gateway"></a>Создание Key Vault, политики и сертификат для использования шлюзом приложений

```azurepowershell
$keyVault = New-AzKeyVault -Name $kv -ResourceGroupName $rgname -Location $location -EnableSoftDelete 
Set-AzKeyVaultAccessPolicy -VaultName $kv -PermissionsToSecrets get -ObjectId $identity.PrincipalId

$policy = New-AzKeyVaultCertificatePolicy -ValidityInMonths 12 `
  -SubjectName "CN=www.contoso11.com" -IssuerName self `
  -RenewAtNumberOfDaysBeforeExpiry 30
$certificate = Add-AzKeyVaultCertificate -VaultName $kv -Name "cert1" -CertificatePolicy $policy
$certificate = Get-AzKeyVaultCertificate -VaultName $kv -Name "cert1"
$secretId = $certificate.SecretId.Replace($certificate.Version, "")
```

### <a name="create-a-vnet"></a>Создание виртуальной сети

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-static-public-vip"></a>Создайте статический общедоступный виртуальный IP-адрес

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-pool-and-frontend-ports"></a>Создание пула и интерфейсную порты

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "port2" -Port 80
```

### <a name="point-ssl-certificate-to-key-vault"></a>Точка SSL-сертификат для хранилища ключей

```azurepowershell
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert1" -KeyVaultSecretId $secretId
```

### <a name="create-listeners-rules-and-autoscale"></a>Создание прослушивателей, правил и автоматического масштабирования

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Https `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "listener2" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02
$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool
$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
```

### <a name="assign-user-managed-identity-to-the-application-gateway"></a>Назначение удостоверения пользователей на шлюзе приложений

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

## <a name="next-steps"></a>Дальнейшие действия

[Дополнительные сведения о моста SSL](ssl-overview.md).