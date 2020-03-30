---
title: Разоблачить сервер WebSocket в шлюз приложений
description: В этой статье содержится информация о том, как выставить сервер WebSocket на шлюз приложения с контроллером входа для кластеров AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1f068c9d98a827afd16da01bdc40cbb6ca5dc465
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297838"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Разоблачить сервер WebSocket в шлюз приложений

Как указано в документации Application Gateway v2 - он [обеспечивает родную поддержку протоколов WebSocket и HTTP/2.](features.md#websocket-and-http2-traffic) Обратите внимание, что как для Application Gateway, так и для Kubernetes Ingress - нет настраиваемой для пользователя настройки, чтобы выборочно включить или отключить поддержку WebSocket.

Развертывание YAML Kubernetes ниже показывает минимальную конфигурацию, используемую для развертывания сервера WebSocket, которая является такой же, как развертывание обычного веб-сервера:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

Учитывая, что все предпосылки выполнены, и у вас есть шлюз приложения, управляемый Kubernetes Ingress в вашем AKS, развертывание выше приведет к `ws.contoso.com` тому, что сервер WebSockets будет выставлен на порту 80 общедоступного IP-адреса вашего приложения Gateway и домена.

Следующая команда cURL протестирует развертывание сервера WebSocket:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>Зонды здоровья WebSocket

Если развертывание не определяет явно зонды работоспособности, шлюз приложения попытается http GET на конечном пункте сервера WebSocket.
В зависимости от реализации сервера[(вот один мы любим)](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)WebSocket конкретные заголовки могут потребоваться (например).`Sec-Websocket-Version`
Поскольку application Gateway не добавляет заголовки WebSocket, ответ зонда работоспособности приложения `400 Bad Request`с вашего сервера WebSocket, скорее всего, будет.
В результате application Gateway отметит ваши стручки как неработоспособные, что в конечном итоге приведет к `502 Bad Gateway` для потребителей сервера WebSocket.
Чтобы избежать этого, возможно, потребуется добавить обработчик`/health` HTTP GET для `200 OK`проверки работоспособности на сервер (например, который возвращается).
