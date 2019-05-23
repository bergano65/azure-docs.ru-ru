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
ms.openlocfilehash: ba679f8b49d212d28ebbdf24e0031d87bef315af
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65765223"
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
