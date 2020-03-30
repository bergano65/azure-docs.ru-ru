---
title: Размещение нескольких сайтов в шлюзе приложений Azure
description: В этой статье предоставлен обзор поддержки нескольких сайтов в Шлюзе приложений Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/11/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 4d945a255dacd35c61c3c80574b7d46b56de4aab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257416"
---
# <a name="application-gateway-multiple-site-hosting"></a>Размещение нескольких сайтов с помощью шлюза приложений

Несколько хостингов позволяют настроить более одного веб-приложения в одном порту шлюза приложения. Эта функция позволяет настроить более эффективную топологию развернутых служб, добавляя до 100 веб-сайтов в один шлюз приложений. Каждый веб-сайт может быть направлен в свой собственный внутренний пул. В следующем примере шлюз `contoso.com` `fabrikam.com` приложений обслуживает трафик для двух серверных пулов бэк-энда, называемых ContosoServerPool и FabrikamServerPool.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> Правила обрабатываются в порядке, указанном на портале v1 SKU. Для v2 SKU точные матчи имеют более высокий приоритет. Мы настоятельно рекомендуем в первую очередь настроить многосайтовые прослушиватели, чтобы настроить базовый прослушиватель.  Это позволит гарантировать, что трафик будет перенаправлен на правильный внутренний сервер. Если базовый прослушиватель стоит первым в списке и совпадает с входящим запросом, он будет обрабатываться прослушивателем.

Запросы для `http://contoso.com` маршрутизируются в ContosoServerPool, а запросы для `http://fabrikam.com` — в FabrikamServerPool.

Аналогичным образом, вы можете разместить несколько поддоменов одного и того же родительского домена в одном и том же развертывании шлюза приложения. Например, можно `http://blog.contoso.com` разместить `http://app.contoso.com` и на одном развертывании шлюза приложения.

## <a name="host-headers-and-server-name-indication-sni"></a>Заголовки узлов и указание имени сервера (SNI)

Существует три распространенных механизма, обеспечивающих размещение нескольких сайтов в одной инфраструктуре:

1. Размещение нескольких веб-приложений с уникальным IP-адресом у каждого из них.
2. Использование имени узла для размещения нескольких веб-приложений на одном IP-адресе.
3. Использование разных портов для размещения нескольких веб-приложений на одном IP-адресе.

В настоящее время Application Gateway поддерживает единый публичный IP-адрес, где он слушает трафик. Таким образом, несколько приложений, каждый со своим IP-адресом в настоящее время не поддерживается. 

Application Gateway поддерживает несколько приложений, каждый из которых прослушивает в разных портах, но этот сценарий требует, чтобы приложения принимали трафик в нестандартных портах. Это часто не конфигурация, что вы хотите.

Шлюз приложений использует заголовки узлов HTTP 1.1 для размещения нескольких веб-сайтов на одном общедоступном IP-адресе и порте. Сайты, размещенные на шлюзе приложений, также могут поддерживать разгрузку TLS с помощью расширения TLS-указания на имя сервера (SNI). В рамках этого сценария это значит, что браузер клиента и внутренняя веб-ферма должны поддерживать HTTP/1.1 и расширение TLS, как определено в стандарте RFC 6066.

## <a name="listener-configuration-element"></a>Элемент конфигурации прослушивателя

Существующие элементы конфигурации HTTPListener дополнены для поддержки элементов указания имени хоста и сервера. Он используется приложением Gateway для направления трафика в соответствующий пул бэкэнда. 

Следующий пример кода — фрагмент элемента Httplisteners из файла шаблона:

```json
"httpListeners": [
    {
        "name": "appGatewayHttpsListener1",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
            },
            "Protocol": "Https",
            "SslCertificate": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
            },
            "HostName": "contoso.com",
            "RequireServerNameIndication": "true"
        }
    },
    {
        "name": "appGatewayHttpListener2",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
            },
            "Protocol": "Http",
            "HostName": "fabrikam.com",
            "RequireServerNameIndication": "false"
        }
    }
],
```

Комплексное развертывание с помощью шаблона можно изучить на примере [шаблона Resource Manager, использующего размещение нескольких сайтов](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting).

## <a name="routing-rule"></a>Правило маршрутизации

В правиле развязывания изменений не требуется. Следует все так же выбрать базовое правило маршрутизации, чтобы привязать соответствующий сайт прослушивателя к нужному внутреннему пулу адресов.

```json
"requestRoutingRules": [
{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

},
{
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}
]
```

## <a name="next-steps"></a>Дальнейшие действия

Изучив размещение нескольких сайтов, перейдите к [созданию шлюза приложений с использованием размещения нескольких сайтов](tutorial-multiple-sites-powershell.md) , чтобы создать шлюз приложений с возможностью поддержки нескольких веб-приложений.

