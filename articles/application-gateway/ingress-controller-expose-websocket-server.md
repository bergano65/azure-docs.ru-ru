---
title: Предоставление серверу WebSocket доступа к шлюзу приложений
description: В этой статье содержатся сведения о том, как предоставить серверу WebSocket доступ к шлюзу приложений с помощью контроллера входящего трафика для кластеров AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 01fde82e69917f59f6519524c4c8828feb84a4f9
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795969"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Предоставление серверу WebSocket доступа к шлюзу приложений

Как описано в документации по шлюзу приложений версии 2, она [обеспечивает встроенную поддержку протоколов WebSocket и HTTP/2](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic). Обратите внимание, что для шлюза приложений и входящих в Kubernetes данных нет настраиваемых пользователем параметров, позволяющих выборочно включать или отключать поддержку WebSocket.

В Kubernetes Deployment YAML ниже показана минимальная конфигурация, используемая для развертывания сервера WebSocket, что аналогично развертыванию обычного веб-сервера.
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

Учитывая, что все необходимые компоненты выполнены и у вас есть шлюз приложений, управляемый Kubernetes входящими в AKS, развертывание выше может привести к тому, что сервер WebSockets будет предоставляться через порт 80 общедоступного IP-адреса шлюза приложений и `ws.contoso.com` поддомен.

Следующая команда заключается в проверке развертывания сервера WebSocket:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>Зонды работоспособности WebSocket

Если в развертывании не определены явно проверки работоспособности, шлюз приложений попытается получить HTTP-запрос в конечной точке сервера WebSocket.
В зависимости от реализации сервера ([вот, что мы любим](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)) могут потребоваться определенные заголовки WebSocket (`Sec-Websocket-Version` для экземпляра).
Так как шлюз приложений не добавляет заголовки WebSocket, скорее всего, на сервере WebSocket отклика проверки работоспособности шлюза приложений будет `400 Bad Request`.
В результате шлюз приложений будет помечать модули Pod как неработоспособные, что в конечном итоге приведет к `502 Bad Gateway` для потребителей сервера WebSocket.
Чтобы избежать этого, может потребоваться добавить обработчик HTTP GET для проверки работоспособности на сервере (`/health` для экземпляра, который возвращает `200 OK`).
