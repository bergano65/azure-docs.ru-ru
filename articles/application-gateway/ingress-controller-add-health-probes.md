---
title: Добавление проб работоспособности в модули AKS Pod
description: В этой статье содержатся сведения о том, как добавить зонды работоспособности (готовность и/или Live) в AKS Pod с помощью шлюза приложений.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795592"
---
# <a name="add-health-probes-to-your-service"></a>Добавление проб работоспособности в службу
По умолчанию входной контроллер выполняет инициализацию HTTP GET для предоставляемых модулей Pod.
Свойства зонда можно настроить, добавив [проверку готовности или динамической проверки](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) в `deployment`/`pod` спецификации.

## <a name="with-readinessprobe-or-livenessprobe"></a>С `readinessProbe` или `livenessProbe`
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

Справочник по API Kubernetes:
* [Зонды контейнеров](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [Действие HttpGet](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe` и `livenessProbe` поддерживаются при настройке с помощью `httpGet`.
> * Проверка на портах, отличных от показанных в модуле Pod, сейчас не поддерживается.
> * `HttpHeaders`, `InitialDelaySeconds``SuccessThreshold` не поддерживаются.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Без `readinessProbe` или `livenessProbe`
Если указанные выше зонды не предоставлены, контроллер входящего трафика предполагала, что служба доступна в `Path`, указанной для `backend-path-prefix` аннотации, или `path`, указанных в определении `ingress` для службы.

## <a name="default-values-for-health-probe"></a>Значения по умолчанию для проверки работоспособности
Для любого свойства, которое не может быть определено с помощью проверки готовности или жизни, устанавливаются значения по умолчанию.

| Свойство проверки шлюза приложений | По умолчанию |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |