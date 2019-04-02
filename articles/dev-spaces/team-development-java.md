---
title: Коллективная разработка с помощью Azure Dev Spaces, Java и VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: stepro
ms.author: stephpr
ms.date: 08/01/2018
ms.topic: tutorial
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: 'Docker, Kubernetes, Azure, служба контейнеров Azure, служба Azure Kubernetes, контейнеры, Helm, сетка службы, сетка службы маршрутизации, kubectl, k8s '
manager: mmontwil
ms.openlocfilehash: 3f7a7b5c9a22ba9cb8746cecde56c0a047521ad0
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437654"
---
[!INCLUDE [](../../includes/devspaces-team-development-1.md)]

### <a name="make-a-code-change"></a>Измените код
В окне VS Code перейдите к `mywebapi` и в `src/main/java/com/ms/sample/mywebapi/Application.java` измените код на метод `String index()`, например так:

```java
@RequestMapping(value = "/", produces = "text/plain")
public String index() {
    return "Hello from mywebapi says something new";
}
```

[!INCLUDE [](../../includes/devspaces-team-development-2.md)]
