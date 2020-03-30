---
title: Используйте частный IP-адрес для внутренней реуктора для точки входа
description: В этой статье содержится информация о том, как использовать частные ИС для внутренней реукторов и тем самым подвергая конечную точку Ingress в кластере остальной части VNet.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795493"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Используйте частный IP для внутренней реукторов для точки входа 

Эта функция позволяет разоблачить конечную `Virtual Network` точку входа в использовании частного IP.

## <a name="pre-requisites"></a>Предварительные требования  
Шлюз приложений с [конфигурацией Private IP](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)

Существует два способа настройки контроллера для использования private IP для входа,

## <a name="assign-to-a-particular-ingress"></a>Назначить конкретному всховщику
Чтобы разоблачить определенный вход через частный [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) IP, используйте аннотацию в Ingress.

### <a name="usage"></a>Использование
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

Для шлюзов приложений без частного IP, `appgw.ingress.kubernetes.io/use-private-ip: "true"` Ingresses аннотированные с будут проигнорированы. Это будет указано в событии входа и журнале AGIC pod.

* Ошибка, указанная в событии Ingress

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Ошибка, указанная в журналах AGIC

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Назначить глобально
В случае, требование заключается в том, чтобы ограничить `appgw.usePrivateIP: true` все `helm` Ingresses подвергаться воздействию частных IP, использование в config.

### <a name="usage"></a>Использование
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Это позволит фильтровать всходной контроллер конфигураций IP-адреса для частного IP при настройке передних слушателей на шлюзе приложения.
AGIC будет паниковать и сбой, если `usePrivateIP: true` и не частный IP назначен.

> [!NOTE]
> Приложение Gateway v2 SKU требует общественного IP. Если вам требуется, чтобы шлюз приложения был закрытым, прикрепите [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) к подсети портала приложения для ограничения трафика.
