---
title: Добавить зонды здоровья к вашим стручкам AKS
description: В этой статье содержится информация о том, как добавить зонды работоспособности (готовность и/или живость) в стручки AKS с шлюзом приложения.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795592"
---
# <a name="add-health-probes-to-your-service"></a>Добавление зондов работоспособности в свой сервис
По умолчанию контроллер Ingress предоставит зонд HTTP GET для открытых стручков.
Свойства зонда можно настроить, добавив [зонд Готовности или Liveness в](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) `deployment` / `pod` свой спецификацию.

## <a name="with-readinessprobe-or-livenessprobe"></a>С `readinessProbe` или`livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Ссылка на API Kubernetes:
* [Контейнерные зонды](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet действий](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe`и `livenessProbe` поддерживаются при `httpGet`настройке с помощью .
> * Зондирование на порту, кроме того, который подвергается на стручке, в настоящее время не поддерживается.
> * `HttpHeaders`, `InitialDelaySeconds` `SuccessThreshold` не поддерживаются.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Без `readinessProbe` или`livenessProbe`
Если вышеуказанные зонды не предоставляются, то Ingress Controller делает `Path` предположение, что услуга достижима по указанной для `backend-path-prefix` аннотации или указанной `path` в `ingress` определении службы.

## <a name="default-values-for-health-probe"></a>Значения по умолчанию для зонда здоровья
Для любого свойства, которое не может быть выведено зондом готовности/живости, устанавливаются значения по умолчанию.

| Свойство зонда шлюза применения | Значение по умолчанию |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |