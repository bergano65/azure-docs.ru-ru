---
title: Заметка контроллера входящих данных шлюза приложений
description: Эта статья содержит документацию по заметкам, относящимся к контроллеру входящего трафика шлюза приложений.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a3583a5efd120733ce7f6b71a7594b5636593f99
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795999"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Заметки для контроллера входящего трафика шлюза приложений 

## <a name="introductions"></a>Описываются

Ресурс входящих данных Kubernetes можно снабдить заметками произвольными парами "ключ-значение". АГИК полагается на аннотации для программирования функций шлюза приложений, которые нельзя настроить с помощью входящего YAML. Пометки входящих данных применяются ко всем параметрам HTTP, внутренним пулам и прослушивателям, производным от входящего ресурса.

## <a name="list-of-supported-annotations"></a>Список поддерживаемых заметок

Для входящего в АГИК ресурса он **должен быть помечен** с помощью `kubernetes.io/ingress.class: azure/application-gateway`. Только после этого АГИК будет работать с рассматриваемым ресурсом.

| Ключ заметки | Тип значения | По умолчанию | Допустимые значения
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#ssl-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32` (секунд) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32` (секунд) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Префикс пути к серверной части

Эта заметка позволяет перезаписывать путь к серверной части, указанный во входящем ресурсе, с префиксом, указанным в этой заметке. Это позволяет пользователям предоставлять службы, конечные точки которых отличаются от имен конечных точек, используемых для предоставления службы в входящем ресурсе.

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
В приведенном выше примере мы определили входной ресурс с именем `go-server-ingress-bkprefix` с заметкой `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"`. Заметка указывает шлюзу приложений создать параметр HTTP, который будет иметь префикс пути, переопределяющий путь `/hello` для `/test/`.

> [!NOTE] 
> В приведенном выше примере определено только одно правило. Однако заметки применяются ко всему входящему ресурсу, поэтому, если пользователь определил несколько правил, префикс пути к внутреннему серверу будет настроен для каждого из указанных путей. Таким образом, если пользователю нужны разные правила с разными префиксами пути (даже для одной службы), им потребуется определить различные входящие ресурсы.

## <a name="ssl-redirect"></a>Перенаправление SSL

Шлюз приложений [можно настроить](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview) для автоматического перенаправления URL-адресов HTTP к их аналогам HTTPS. Если эта заметка имеется и протокол TLS настроен правильно, контроллер входящего трафика Kubernetes создаст [правило маршрутизации с конфигурацией перенаправления](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration) и применит изменения к шлюзу приложений. Созданное перенаправление будет HTTP-`301 Moved Permanently`.

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

## <a name="connection-draining"></a>Сток подключений

`connection-draining`: Эта аннотация позволяет пользователям указать, следует ли включить сток подключений.
`connection-draining-timeout`: Эта аннотация позволяет пользователям указать время ожидания, по истечении которого шлюз приложений будет завершать запросы конечной точки внутреннего сервера.

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

## <a name="cookie-based-affinity"></a>Сходство на основе файлов cookie

Эта заметка позволяет указать, следует ли включить сходство на основе файлов cookie.

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

Эта заметка позволяет указать время ожидания запроса в секундах, по истечении которого шлюз приложений не сможет выполнить запрос, если ответ не получен.

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

## <a name="use-private-ip"></a>Использовать частный IP-адрес

Эта заметка позволяет указать, следует ли предоставлять эту конечную точку на частном IP-адресе шлюза приложений.

> [!NOTE]
> * Шлюз приложений не поддерживает несколько IP-адресов на одном порту (например: 80/443). Входящий в состав аннотации `appgw.ingress.kubernetes.io/use-private-ip: "false"` а другой с `appgw.ingress.kubernetes.io/use-private-ip: "true"` в `HTTP` приведет к сбою АГИК при обновлении шлюза приложений.
> * Для шлюза приложений, у которого нет частного IP-адреса, передает с `appgw.ingress.kubernetes.io/use-private-ip: "true"` будет игнорироваться. Это будет отражено в журналах контроллера и входящих событий для этих передает с предупреждением `NoPrivateIP`.


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

## <a name="backend-protocol"></a>Внутренний протокол

Эта заметка позволяет указать протокол, который должен использоваться шлюзом приложений при взаимодействии с модулями Pod. Поддерживаемые протоколы: `http`, `https`

> [!NOTE]
> * Несмотря на то, что самозаверяющие сертификаты поддерживаются на шлюзе приложений, в настоящее время АГИК поддерживают только `https`, когда модули Pod используют сертификат, подписанный известным центром сертификации.
> * Не следует использовать порт 80 с протоколом HTTPS и портом 443 с HTTP для модулей Pod.

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