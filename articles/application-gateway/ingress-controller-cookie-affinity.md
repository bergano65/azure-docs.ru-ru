---
title: Включить сродство на основе файлов cookie с шлюзом приложений
description: В этой статье содержится информация о том, как включить сродство на основе файлов cookie с шлюзом приложения.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: adacd805e736524fd7956c4bbc0ad402980b4cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795976"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Включить cookie основе сродства с приложением шлюз
Как указано в [документации Azure Application Gateway,](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)Application Gateway поддерживает сродство на основе файлов cookie, что означает, что он может направлять последующий трафик от сеанса пользователя к тому же серверу для обработки.

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
