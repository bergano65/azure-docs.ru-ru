---
title: Защита веб-приложений с помощью Шлюза приложений Azure в PowerShell
description: Эта статья содержит рекомендации о том, как настроить веб-приложения в качестве узлов серверной части на имеющемся или новом шлюзе приложений.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
origin.date: 10/16/2018
ms.date: 03/12/2019
ms.author: v-junlch
ms.openlocfilehash: dcf21fe111ab742074ab4fe580a021338e1f7c43
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122223"
---
# <a name="configure-app-service-with-application-gateway"></a>Настройка службы приложений с помощью шлюза приложений

Шлюз приложений позволяет иметь приложение службы приложений или другую мультитенантную службу в качестве участника узла серверной части. В этой статье вы узнаете, как настраивать приложения службы приложений с помощью шлюза приложений. Первый пример показывает, как настроить шлюз приложений для использования в качестве участника пула серверной части веб-приложения. Второй пример показывает, как создать шлюз приложений с помощью веб-приложения в качестве участника пула серверной части.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-web-app-behind-an-existing-application-gateway"></a>Настройка веб-приложения для имеющегося шлюза приложения

В следующем примере в имеющийся шлюз приложений добавляется веб-приложение в качестве участника пула серверной части. Для работы веб-приложений необходимо указать параметр `-PickHostNamefromBackendHttpSettings` в конфигурации пробы и `-PickHostNameFromBackendAddress` в параметрах HTTP серверной части.

```powershell
# FQDN of the web app
$webappFQDN = "<enter your webapp FQDN i.e mywebsite.chinacloudsites.cn>"

# Retrieve the resource group
$rg = Get-AzResourceGroup -Name 'your resource group name'

# Retrieve an existing application gateway
$gw = Get-AzApplicationGateway -Name 'your application gateway name' -ResourceGroupName $rg.ResourceGroupName

# Define the status codes to match for the probe
$match=New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399

# Add a new probe to the application gateway
Add-AzApplicationGatewayProbeConfig -name webappprobe2 -ApplicationGateway $gw -Protocol Http -Path / -Interval 30 -Timeout 120 -UnhealthyThreshold 3 -PickHostNameFromBackendHttpSettings -Match $match

# Retrieve the newly added probe
$probe = Get-AzApplicationGatewayProbeConfig -name webappprobe2 -ApplicationGateway $gw

# Configure an existing backend http settings
Set-AzApplicationGatewayBackendHttpSettings -Name appGatewayBackendHttpSettings -ApplicationGateway $gw -PickHostNameFromBackendAddress -Port 80 -Protocol http -CookieBasedAffinity Disabled -RequestTimeout 30 -Probe $probe

# Add the web app to the backend pool
Set-AzApplicationGatewayBackendAddressPool -Name appGatewayBackendPool -ApplicationGateway $gw -BackendFqdns $webappFQDN

# Update the application gateway
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="configure-a-web-application-behind-a-new-application-gateway"></a>Настройка веб-приложения для нового шлюза приложения

В этом сценарии веб-приложение развертывается с помощью примера веб-сайта asp.net и шлюза приложения.

```powershell
# Defines a variable for a dotnet get started web app repository location
$gitrepo="https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git"

# Unique web app name
$webappname="mywebapp$(Get-Random)"

# Creates a resource group
$rg = New-AzResourceGroup -Name ContosoRG -Location ChinaNorth

# Create an App Service plan in Free tier.
New-AzAppServicePlan -Name $webappname -Location ChinaNorth -ResourceGroupName $rg.ResourceGroupName -Tier Free

# Creates a web app
$webapp = New-AzWebApp -ResourceGroupName $rg.ResourceGroupName -Name $webappname -Location ChinaNorth -AppServicePlan $webappname

# Configure GitHub deployment from your GitHub repo and deploy once to web app.
$PropertiesObject = @{
    repoUrl = "$gitrepo";
    branch = "master";
    isManualIntegration = "true";
}
Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName $rg.ResourceGroupName -ResourceType Microsoft.Web/sites/sourcecontrols -ResourceName $webappname/web -ApiVersion 2015-08-01 -Force

# Creates a subnet for the application gateway
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Creates a vnet for the application gateway
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName $rg.ResourceGroupName -Location ChinaNorth -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Retrieve the subnet object for use later
$subnet=$vnet.Subnets[0]

# Create a public IP address
$publicip = New-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -name publicIP01 -location ChinaNorth -AllocationMethod Dynamic

# Create a new IP configuration
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

# Create a backend pool with the hostname of the web app
$pool = New-AzApplicationGatewayBackendAddressPool -Name appGatewayBackendPool -BackendFqdns $webapp.HostNames

# Define the status codes to match for the probe
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399

# Create a probe with the PickHostNameFromBackendHttpSettings switch for web apps
$probeconfig = New-AzApplicationGatewayProbeConfig -name webappprobe -Protocol Http -Path / -Interval 30 -Timeout 120 -UnhealthyThreshold 3 -PickHostNameFromBackendHttpSettings -Match $match

# Define the backend http settings
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name appGatewayBackendHttpSettings -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120 -PickHostNameFromBackendAddress -Probe $probeconfig

# Create a new front-end port
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 80

# Create a new front end IP configuration
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Create a new listener using the front-end ip configuration and port created earlier
$listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Create a new rule
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Define the application gateway SKU to use
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# Create the application gateway
$appgw = New-AzApplicationGateway -Name ContosoAppGateway -ResourceGroupName $rg.ResourceGroupName -Location ChinaNorth -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -Probes $probeconfig -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="get-application-gateway-dns-name"></a>Получение DNS-имени шлюза приложений

После создания шлюза следует настроить внешний интерфейс для обмена данными. Если вы используете общедоступный IP-адрес, шлюзу приложений требуется динамически назначаемое непонятное имя DNS. Чтобы гарантировать попадание пользователей на шлюз приложений, можно использовать запись CNAME, чтобы указать общедоступную конечную точку шлюза приложений. Чтобы создать псевдоним, извлеките подробные сведения о шлюзе приложений и соответствующее IP- или DNS-имя с помощью элемента PublicIPAddress, связанного со шлюзом приложений. Это можно сделать с помощью Azure DNS или других поставщиков DNS, создав запись CNAME, которая указывает на [общедоступный IP-адрес](../dns/dns-custom-domain.md#public-ip-address). Использование записи A не рекомендуется, так как виртуальный IP-адрес может измениться после перезапуска приложения шлюза.

```powershell
Get-AzPublicIpAddress -ResourceGroupName ContosoRG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : ContosoRG
Location                 : chinanorth
Id                       : /subscriptions/<subscription_id>/resourceGroups/ContosoRG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/ContosoAppGateway/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.chinacloudapp.cn"
                            }
```

## <a name="restrict-access"></a>Ограничение доступа

Веб-приложения, развернутые в этих примерах, используют общедоступные IP-адреса, доступ к которым можно получить напрямую из Интернета. Это помогает устранить неполадки, когда вы знакомитесь с новыми функциями и пробуете новинки. Однако, если вы хотите развернуть функцию в производственной среде, необходимо добавить дополнительные ограничения.

Один из способов ограничения доступа к веб-приложению — использовать [ограничения статических IP-адресов в Службе приложений Azure](../app-service/app-service-ip-restrictions.md). Например, можно ограничить веб-приложение, чтобы получать трафик только из шлюза приложения. Используйте функцию ограничения IP-адреса для службы приложения, чтобы сделать виртуальный IP-адрес шлюза приложения единственным адресом с доступом.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как настроить перенаправление, в следующем разделе: [Настройка перенаправления в шлюзе приложений с помощью PowerShell](redirect-overview.md).

<!-- Update_Description: code update -->