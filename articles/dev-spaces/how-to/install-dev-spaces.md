---
title: Включение Azure Dev Spaces в Службе Azure Kubernetes и установка средств на стороне клиента
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Сведения о том, как включить Azure Dev Spaces в кластере AKS и установить средства на стороне клиента.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: b62c4a4861529c19363f159b8cc64a32a0ba11e8
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996267"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Включение Azure Dev Spaces в кластере AKS и установка средств на стороне клиента

В этой статье представлены несколько методов, позволяющих включить Azure Dev Spaces в кластере AKS и установить средства на стороне клиента.

## <a name="enable-or-remove-azure-dev-spaces-using-the-cli"></a>Включение и удаление Azure Dev Spaces с помощью CLI

Чтобы включить Dev Spaces с помощью интерфейса командной строки (CLI), вам потребуется следующее:
* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись][az-portal-create-account].
* [Установленный Azure CLI][install-cli].
* [Кластер AKS][create-aks-cli] в [поддерживаемом регионе][supported-regions].

С помощью команды `use-dev-spaces` включите Dev Spaces в кластере AKS и следуйте инструкциям на экране.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Приведенная выше команда включает Dev Spaces в кластере *myAKSCluster* в группе *myResourceGroup* и создает пространство разработки *по умолчанию*.

```console
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

Чтобы удалить Azure Dev Spaces из кластера AKS, используйте команду `azds remove`. Пример:

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Приведенная выше команда удаляет Azure Dev Spaces из кластера *MyAKS* в *MyResourceGroup*. Любое пространство имен, созданное с помощью Azure Dev Spaces, будет сохраняться вместе с его рабочими нагрузками, но для новых рабочих нагрузок в этих пространствах имен инструментирование Azure Dev Spaces применяться не будет. Кроме того, при перезапуске существующих pod, инструментируемых с помощью Azure Dev Spaces, могут возникать ошибки. Такие pod необходимо повторно развернуть без инструментов Azure Dev Spaces. Чтобы полностью удалить Azure Dev Spaces из кластера, удалите все pod из всех пространств имен, где ранее была включена служба Azure Dev Spaces.

## <a name="install-the-client-side-tools"></a>Установка средств на стороне клиента

Клиентские средства Azure Dev Spaces позволяют взаимодействовать со средой Dev Spaces в кластере AKS с локального компьютера. Существует несколько способов установки средств на стороне клиента.

* Установите [расширение Azure Dev Spaces][vscode-extension] для [Visual Studio Code][vscode].
* Для [Visual Studio 2019][visual-studio] установите рабочую нагрузку "Разработка для Azure".
* Скачайте и установите CLI для ОС [Windows][cli-win], [Mac][cli-mac] или [Linux][cli-linux].

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как в Azure Dev Spaces можно разрабатывать более сложные приложения в нескольких контейнерах и как упростить совместную разработку, используя разные версии и ветви кода в разных средах.

> [!div class="nextstepaction"]
> [Краткое руководство. Коллективная разработка в Kubernetes с использованием Azure Dev Spaces][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
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
