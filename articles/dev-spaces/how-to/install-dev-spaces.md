---
title: Установка Azure Dev Spaces в AKS & средств на стороне клиента
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Узнайте, как установить Azure Dev Spaces в кластере AKS и установить клиентские средства.
keywords: Docker, Kubernetes, Azure, служба контейнеров Azure, служба Azure Kubernetes, контейнеры, Helm, сетка службы, сетка службы маршрутизации, kubectl, k8s
ms.openlocfilehash: 2649b36c96313d4a7d878a1c72c3b175ad0f4d30
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325777"
---
# <a name="install-azure-dev-spaces-on-aks-and-the-client-side-tooling"></a>Установка Azure Dev Spaces в AKS и клиентские средства на стороне клиента

В этой статье показано несколько способов установки Azure Dev Spaces в кластере AKS, а также установка клиентских средств на стороне клиента.

## <a name="install-azure-dev-spaces-using-the-cli"></a>Установка Azure Dev Spaces с помощью интерфейса командной строки

Прежде чем можно будет установить модули разработки с помощью интерфейса командной строки, вам потребуется:
* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись][az-portal-create-account].
* [Azure CLI установлен][install-cli].
* [Кластер AKS][create-aks-cli] в [поддерживаемом регионе][supported-regions].

С помощью команды `use-dev-spaces` включите Dev Spaces в кластере AKS и следуйте инструкциям на экране.

```cmd
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Приведенная выше команда включает пространства разработки в кластере *myAKSCluster* в группе *myResourceGroup* и создает пространство разработки *по умолчанию* .

```cmd
$ az aks use-dev-spaces -g myResourceGroup -n myAKSCluster

'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

Команда `use-dev-spaces` также устанавливает интерфейс командной строки Azure Dev Spaces.

## <a name="install-azure-dev-spaces-using-the-azure-portal"></a>Установка Azure Dev Spaces с помощью портал Azure

Прежде чем можно будет установить модули разработки с помощью портал Azure, вам потребуется:
* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись][az-portal-create-account].
* [Кластер AKS][create-aks-portal] в [поддерживаемом регионе][supported-regions].

Чтобы установить Azure Dev Spaces с помощью портал Azure, выполните следующие действия.
1. Войдите на [портале Azure][az-portal].
1. Перейдите к кластеру AKS.
1. Щелкните *пространства разработки*.
1. Под надписью *Enable Dev Spaces* (Включить Dev Spaces) нажмите *Да* и щелкните *Сохранить*.

![Включение Dev Spaces на портале Azure](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

При установке Azure Dev Spaces с помощью **портал Azure не** устанавливаются средства на стороне клиента для Azure dev Spaces.

## <a name="install-the-client-side-tooling"></a>Установка клиентских средств

Для взаимодействия с пространствами разработки в кластере AKS с локального компьютера можно использовать средства клиентской части Azure Dev Spaces. Существует несколько способов установки клиентских средств.

* В [Visual Studio Code][vscode]установите [расширение Azure dev Spaces][vscode-extension].
* В [Visual Studio 2019][visual-studio]установите рабочую нагрузку разработка для Azure.
* В Visual Studio 2017 установите рабочую нагрузку веб-разработки и [средства Visual Studio для Kubernetes][visual-studio-k8s-tools].
* Скачайте и установите интерфейс командной строки [Windows][cli-win], [Mac][cli-mac]или [Linux][cli-linux] .

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как в Azure Dev Spaces можно разрабатывать более сложные приложения в нескольких контейнерах и как упростить совместную разработку, используя разные версии и ветви кода в разных средах.

> [!div class="nextstepaction"]
> [Краткое руководство. Коллективная разработка в Kubernetes с использованием Azure Dev Spaces][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
