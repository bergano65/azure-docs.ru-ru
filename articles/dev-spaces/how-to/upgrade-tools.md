---
title: Обновление средств Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/03/2018
ms.topic: conceptual
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers
ms.openlocfilehash: 24a66c5218bb75672ae784b19213116efdb83690
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232452"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Обновление средств Azure Dev Spaces

Если вы уже используете Azure Dev Spaces, при выходе нового выпуска вам следует обновить клиентские средства Azure Dev Spaces.

## <a name="update-the-azure-cli"></a>Обновление Azure CLI

При обновлении до последней версии Azure CLI вы автоматически получаете последнюю версию расширения интерфейса командной строки для Azure Dev Spaces.

Предыдущую версию удалять не нужно, просто найдите нужный файл для скачивания на странице [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Обновление расширения CLI для Azure Dev Spaces и средств командной строки

Выполните следующую команду:

```cmd
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>Обновление расширения VS Code

После установки расширение автоматически обновляется. Возможно, потребуется перезагрузить расширение, чтобы использовать новые возможности. В VS Code откройте панель **Расширения**, выберите расширения **Azure Dev Spaces** и щелкните **Перезагрузить**.

## <a name="update-the-visual-studio-extension"></a>Обновление расширения Visual Studio

Как и для других расширений и обновлений, Visual Studio сообщит вам о наличии обновления средств Visual Studio для Kubernetes, в пакет которых входит Azure Dev Spaces. Найдите значок флага в верхней правой части экрана.

Чтобы обновить средства в Visual Studio, выберите пункты меню **Сервис > Расширения и обновления**, а затем слева выберите **Обновления**. Найдите **Средства Visual Studio для Kubernetes** и нажмите кнопку **Обновить**.

## <a name="next-steps"></a>Следующие шаги

Протестируйте новые средства, создав новый кластер. Изучите руководства по [Azure Dev Spaces](/azure/dev-spaces).