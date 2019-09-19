---
title: Поддержка WebSocket в шлюзе приложений Azure| Документация Майкрософт
description: Здесь представлен обзор поддержки WebSocket в шлюзе приложений.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/18/2019
ms.openlocfilehash: a48f1b6e4410820d40ba6563d431c690ab791ff0
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097244"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Общие сведения о поддержке WebSocket в шлюзе приложений

Шлюз приложений обеспечивает встроенную поддержку WebSocket независимо от размера. Настраиваемый пользователем параметр для выборочного включения или отключения поддержки WebSocket отсутствует. 

Протокол WebSocket, стандартизированный в [RFC6455](https://tools.ietf.org/html/rfc6455), обеспечивает полнодуплексную связь между сервером и клиентом через длительное TCP-подключение. Эта функция обеспечивает лучшее интерактивное взаимодействие между веб-сервером и клиентом, которое может быть двунаправленным, без необходимости выполнять опрос, как в реализациях на основе HTTP. В отличие от HTTP протокол WebSocket обеспечивает низкие издержки и может повторно использовать одно и то же TCP-подключение для нескольких запросов и ответов, благодаря чему ресурсы используются более эффективно. Протоколы WebSocket работают через традиционные HTTP-порты 80 и 443.

Вы можете продолжать использовать стандартный прослушиватель HTTPListener на порте 80 или 443 для получения трафика WebSocket. Затем этот трафик направляется на внутренний сервер с поддержкой WebSocket с использованием соответствующего серверного пула, как определено в правилах шлюза приложений. Внутренний сервер должен отвечать на проверки работоспособности шлюза приложений. Описание проверок см. в статье [Обзор мониторинга работоспособности шлюза приложений](application-gateway-probe-overview.md). Проверки работоспособности шлюза приложений выполняются только по протоколам HTTP или HTTPS. Каждый внутренний сервер должен отвечать на проверки HTTP шлюза приложений для маршрутизации трафика WebSocket на сервер.

Он используется в приложениях, которые используют преимущества быстрого взаимодействия в реальном времени, такие как чат, панель мониторинга и игровые приложения.

## <a name="how-does-websocket-work"></a>Как работает WebSocket

Чтобы установить соединение WebSocket, между клиентом и сервером осуществляется обмен конкретными подтверждениями на основе HTTP. В случае успешного выполнения протокол уровня приложения обновляется с HTTP на WebSockets, используя ранее установленное подключение TCP. В этом случае протокол HTTP полностью истекает из изображения. данные могут быть отправлены или получены с помощью протокола WebSocket обеими конечными точками, пока соединение WebSocket не будет закрыто. 

![WebSocket](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Элемент конфигурации прослушивателя

Для поддержки трафика WebSocket можно использовать имеющийся прослушиватель HTTPListener. Ниже приведен фрагмент кода с элементом httpListeners из примера файла шаблона. Для поддержки и защиты трафика WebSocket необходимы прослушиватели HTTP и HTTPS. Аналогичным образом можно использовать портал или Azure PowerShell для создания шлюза приложений с прослушивателями на порте 80/443 для поддержки трафика WebSocket.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>BackendAddressPool, BackendHttpSetting и конфигурация правила маршрутизации

Для определения серверного пула с серверами с поддержкой WebSocket используется параметр BackendAddressPool. Параметр backendHttpSetting определяется с внутренним портом 80 или 443. Значение времени ожидания запроса в параметрах HTTP также применяется к сеансу WebSocket. В правиле маршрутизации не требуется никаких изменений, которые используются для привязки соответствующего прослушивателя к соответствующему внутреннему пулу адресов. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>Сервер с поддержкой WebSocket

Для работы сервера с поддержкой WebSocket необходимо, чтобы веб-сервер HTTP или HTTPS работал на настроенном порте (обычно 80 или 443). Это требование связано с тем, что протокол WebSocket требует первоначального подтверждения в виде обновления протокола HTTP до WebSocket. При этом последний должен быть указан как поле заголовка. Ниже приведен пример заголовка:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Другой причиной является то, что проверка работоспособности серверного шлюза приложений поддерживает только протоколы HTTP и HTTPS. Если внутренний сервер не отвечает на проверки по протоколу HTTP или HTTPS, он удаляется из внутреннего пула.

## <a name="next-steps"></a>Следующие шаги

Ознакомившись с поддержкой протокола WebSocket, приступите к [созданию шлюза приложений](quick-create-powershell.md) , чтобы начать работу с веб-приложением с поддержкой WebSocket.