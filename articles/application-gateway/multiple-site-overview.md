---
title: Размещение нескольких сайтов в шлюзе приложений Azure
description: В этой статье предоставлен обзор поддержки нескольких сайтов в Шлюзе приложений Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/11/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: c43ac0923e0d3d76c25657f4870a0a0431bc8b6e
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096430"
---
# <a name="application-gateway-multiple-site-hosting"></a>Размещение нескольких сайтов с помощью шлюза приложений

Размещение нескольких сайтов позволяет настроить несколько веб-приложений на одном и том же порту шлюза приложений. Эта функция позволяет настроить более эффективную топологию развернутых служб, добавляя до 100 веб-сайтов в один шлюз приложений. Каждый веб-сайт может быть направлен в свой собственный внутренний пул. В следующем примере шлюз приложений обслуживает трафик для `contoso.com` и `fabrikam.com` из двух пулов внутренних серверов, именуемых ContosoServerPool и FabrikamServerPool.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> Правила обрабатываются в том порядке, в котором они указаны на портале для номера SKU v1. Для SKU версии 2 точные совпадения имеют более высокий приоритет. Мы настоятельно рекомендуем в первую очередь настроить многосайтовые прослушиватели, чтобы настроить базовый прослушиватель.  Это позволит гарантировать, что трафик будет перенаправлен на правильный внутренний сервер. Если базовый прослушиватель стоит первым в списке и совпадает с входящим запросом, он будет обрабатываться прослушивателем.

Запросы для `http://contoso.com` маршрутизируются в ContosoServerPool, а запросы для `http://fabrikam.com` — в FabrikamServerPool.

Аналогичным образом можно разместить несколько поддоменов одного и того же родительского домена в одном развертывании шлюза приложений. Например, можно разместить `http://blog.contoso.com` и `http://app.contoso.com` в одном развертывании шлюза приложений.

## <a name="host-headers-and-server-name-indication-sni"></a>Заголовки узлов и указание имени сервера (SNI)

Существует три распространенных механизма, обеспечивающих размещение нескольких сайтов в одной инфраструктуре:

1. Размещение нескольких веб-приложений с уникальным IP-адресом у каждого из них.
2. Использование имени узла для размещения нескольких веб-приложений на одном IP-адресе.
3. Использование разных портов для размещения нескольких веб-приложений на одном IP-адресе.

В настоящее время шлюз приложений поддерживает один общедоступный IP-адрес, по которому он прослушивает трафик. В настоящее время несколько приложений, каждый из которых имеет свой IP-адрес, не поддерживается. 

Шлюз приложений поддерживает несколько приложений, прослушиваемых разными портами, но этот сценарий требует, чтобы приложения принимали трафик на нестандартных портах. Часто это не нужная конфигурация.

Шлюз приложений использует заголовки узлов HTTP 1.1 для размещения нескольких веб-сайтов на одном общедоступном IP-адресе и порте. Сайты, размещенные в шлюзе приложений, могут также поддерживать разгрузку SSL с использованием расширения TLS для указания имени сервера (SNI). В рамках этого сценария это значит, что браузер клиента и внутренняя веб-ферма должны поддерживать HTTP/1.1 и расширение TLS, как определено в стандарте RFC 6066.

## <a name="listener-configuration-element"></a>Элемент конфигурации прослушивателя

Существующие элементы конфигурации HTTPListener расширены для поддержки имен узлов и элементов индикации имени сервера. Он используется шлюзом приложений для маршрутизации трафика в соответствующий внутренний пул. 

Следующий пример кода является фрагментом элемента HttpListeners из файла шаблона:

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

В правиле маршрутизации не требуется никаких изменений. Следует все так же выбрать базовое правило маршрутизации, чтобы привязать соответствующий сайт прослушивателя к нужному внутреннему пулу адресов.

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

