---
title: Аннотации контроллера входа в приложение
description: В этой статье приводится документация по аннотациям, характерным для контроллера входа в приложение.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f54381ddcd11a2e4a24d30d812468da85b5403de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335826"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Аннотации для контроллера входа в шлюз приложений 

## <a name="introductions"></a>Введения

Ресурс Kubernetes Ingress может быть аннотирован произвольными парами ключей/значений. AGIC полагается на аннотации к функциям программы Application Gateway, которые не настраиваются через Ingress YAML. Аннотации ingress применяются ко всем параметрам HTTP, пулам бэкэнда и слушателям, полученным из ресурса входа.

## <a name="list-of-supported-annotations"></a>Список поддерживаемых аннотаций

Для того, чтобы ресурс Ingress наблюдался AGIC, он `kubernetes.io/ingress.class: azure/application-gateway`должен быть **аннотирован** . Только тогда AGIC будет работать с ресурсом Ingress, о котором идет речь.

| Клавиша аннотации | Тип значения | Значение по умолчанию | Допустимые значения
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32`(секунды) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32`(секунды) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Предикстам путь бэкэнда

Эта аннотация позволяет переписать обратный путь, указанный в ресурсе входа, с префиксом, указанным в этой аннотации. Это позволяет пользователям разоблачать службы, конечные точки которых отличаются от названий конечных точек, используемых для разоблачения службы в ресурсе входа.

### <a name="usage"></a>Использование

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>Пример

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
В приведенном выше примере мы определили ресурс `go-server-ingress-bkprefix` входа `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` с аннотацией. Аннотация говорит шлюзу приложения для создания настройки HTTP, которая будет `/hello` `/test/`иметь префикс пути переопределить для пути к .

> [!NOTE] 
> В приведенном выше примере у нас есть только одно правило определено. Тем не менее, аннотации применимы ко всему ресурсу входа, так что если пользователь определил несколько правил, префикс пути бэкэнда будет создан для каждого из указанных путей. Таким образом, если пользователю нужны разные правила с разными префиксами пути (даже для одной и той же службы), ему необходимо будет определить различные ресурсы входа.

## <a name="tls-redirect"></a>Перенаправление TLS

Портал приложений [может быть настроен для](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview) автоматического перенаправления URL-адресов HTTP на их аналоги HTTPS. Когда эта аннотация присутствует и TLS правильно настроен, контроллер Kubernetes Ingress создаст [правило маршрутики с конфигурацией перенаправления](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration) и применит изменения к шлюзу приложения. Созданный перенаправление будет `301 Moved Permanently`HTTP .

### <a name="usage"></a>Использование

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>Пример

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>Слив соединения

`connection-draining`: Эта аннотация позволяет пользователям указать, включать ли соединение слива.
`connection-draining-timeout`: Эта аннотация позволяет пользователям указать тайм-аут, после которого шлюз приложения прекратит запросы в сливную конечную точку бэкэнда.

### <a name="usage"></a>Использование

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>Пример

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>Cookie на основе affinity

Эта аннотация позволяет указать, включать ли сродство на основе файлов cookie.

### <a name="usage"></a>Использование

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>Пример

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>Истекло время ожидания запроса

Эта аннотация позволяет указать тайм-аут запроса в считанные секунды, после чего шлюз приложения не сдаст запрос, если ответ не получен.

### <a name="usage"></a>Использование

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>Пример

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>Использование частного IP

Эта аннотация позволяет указать, следует ли разоблачать эту конечную точку на private IP-адреса Прикладного шлюза.

> [!NOTE]
> * Приложение Gateway не поддерживает несколько ИП в одном порту (пример: 80/443). Вход с `appgw.ingress.kubernetes.io/use-private-ip: "false"` аннотацией `appgw.ingress.kubernetes.io/use-private-ip: "true"` `HTTP` и другой с на вызовет AGIC на провал в обновлении приложения шлюза.
> * Для шлюза приложения, у которого нет `appgw.ingress.kubernetes.io/use-private-ip: "true"` частного IP-адреса, Ingresses с ними будут проигнорированы. Это будет отражено в журналах контроллера и вхотливые события для тех, кто входит с `NoPrivateIP` предупреждением.


### <a name="usage"></a>Использование
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>Пример
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>Протокол бэкэнда

Эта аннотация позволяет указать протокол, который при общении должен использовать шлюз приложений во время разговора с стручками. Поддерживаемые протоколы: `http`,`https`

> [!NOTE]
> * В то время как самоподписанные сертификаты поддерживаются `https` на шлюзе приложения, в настоящее время AGIC поддерживает только тогда, когда Pods используют сертификат, подписанный известным ЦС.
> * Убедитесь в том, чтобы не использовать порт 80 с HTTPS и порт 443 с HTTP на стручках.

### <a name="usage"></a>Использование
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>Пример
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```