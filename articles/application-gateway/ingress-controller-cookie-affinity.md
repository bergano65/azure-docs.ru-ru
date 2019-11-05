---
title: Включение сходства на основе файлов cookie с помощью шлюза приложений
description: В этой статье содержатся сведения о том, как включить сходство на основе файлов cookie с шлюзом приложений.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: cb1a403888f8dca78d1af7395095aa2bac39935e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513579"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Включение сходства на основе файлов cookie с шлюзом приложений
Как описано в [документации по шлюзу приложений Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings), шлюз приложений поддерживает сопоставление на основе файлов cookie. Это означает, что он может направить последующий трафик из сеанса пользователя на тот же сервер для обработки.

## <a name="example"></a>Пример
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```
