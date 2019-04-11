---
title: Коллективная разработка с помощью Azure Dev Spaces, Node.js и VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: 'Docker, Kubernetes, Azure, служба контейнеров Azure, служба Azure Kubernetes, контейнеры, Helm, сетка службы, сетка службы маршрутизации, kubectl, k8s '
ms.openlocfilehash: d8c3ae10e770533c910583e6e2bc218d78df4339
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360963"
---
[!INCLUDE [](../../includes/devspaces-team-development-1.md)]

### <a name="make-a-code-change"></a>Измените код
В окне VS Code перейдите к `mywebapi` и в `server.js` измените код в обработчике GET по умолчанию `/`, например так:

```javascript
app.get('/', function (req, res) {
    res.send('mywebapi now says something new');
});
```

[!INCLUDE [](../../includes/devspaces-team-development-2.md)]
