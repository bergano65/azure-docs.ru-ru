---
title: Использовать частный IP-адрес для внутренней маршрутизации входной конечной точки
description: В этой статье содержатся сведения о том, как использовать частные IP-адреса для внутренней маршрутизации и таким образом предоставлять конечную точку входящих данных в кластере остальной части виртуальной сети.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: 13b8cc5c346febe9bd6e86be1ad8157464c83536
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513228"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Использовать частный IP-адрес для внутренней маршрутизации для конечной точки входящего трафика 

Эта функция позволяет предоставлять конечную точку входящих данных в `Virtual Network` с помощью частного IP-адреса.

## <a name="pre-requisites"></a>Предварительные требования  
Шлюз приложений с [частной IP-конфигурацией](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)

Существует два способа настройки контроллера для использования частного IP-адреса для входящего трафика,

## <a name="assign-to-a-particular-ingress"></a>Назначить определенному входящему
Чтобы предоставить определенный входящий трафик через частный IP-адрес, используйте аннотацию [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) в качестве входных данных.

### <a name="usage"></a>Использование
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

Для шлюзов приложений без частного IP-адреса передает, помеченные `appgw.ingress.kubernetes.io/use-private-ip: "true"`, будут игнорироваться. Это будет указано в журнале входящих событий и АГИК Pod.

* Ошибка, как указано в событии входящего трафика

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Ошибка, как указано в журналах АГИК

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Назначить глобально
В этом случае необходимо ограничить доступ всех передает к частному IP-адресу, использовать `appgw.usePrivateIP: true` в `helm` config.

### <a name="usage"></a>Использование
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Это сделает контроллер входящего трафика фильтром IP-адресов для частного IP-адреса при настройке интерфейсных прослушивателей в шлюзе приложений.
АГИК выдаст сбой, `usePrivateIP: true` если не назначен частный IP-адрес.

> [!NOTE]
> Для SKU шлюза приложений версии 2 требуется общедоступный IP-адрес. Если требуется, чтобы шлюз приложений был частным, подключите [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) к подсети шлюза приложений, чтобы ограничить трафик.
