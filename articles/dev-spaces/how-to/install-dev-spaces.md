---
title: Включите пространства Azure Dev на AKS & установку инструментов со стороны клиента
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Узнайте, как включить пространства Azure Dev в кластере AKS и установить инструменты со стороны клиента.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: a6b3be5ceba5e60b99b2f75e060f3321cd3151f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898951"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Включите пространства Azure Dev в кластере AKS и установите инструменты со стороны клиента

В этой статье показано несколько способов включения пространства Azure Dev в кластер ездовые AKS, а также установка инструментов со стороны клиента.

## <a name="enable-or-remove-azure-dev-spaces-using-the-cli"></a>Включить или удалить пробелы Azure Dev с помощью CLI

Прежде чем включить Dev Spaces с помощью CLI, вам нужно:
* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись][az-portal-create-account].
* [Установлен Azure CLI][install-cli].
* [Кластер AKS][create-aks-cli] в [поддерживаемом регионе.][supported-regions]

С помощью команды `use-dev-spaces` включите Dev Spaces в кластере AKS и следуйте инструкциям на экране.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Вышеупомянутая команда позволяет Dev Spaces в кластере *myAKSCluster* в группе *myResourceGroup* и создает пространство разработчиков *по умолчанию.*

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

Команда `use-dev-spaces` также устанавливает CLI пространства Azure Dev.

Чтобы удалить пробелы Azure Dev из `azds remove` кластера AKS, используйте команду. Пример:

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Вышеупомянутая команда удаляет пространства Azure Dev из кластера *MyAKS* в *MyResourceGroup.* Любые пространства имен, созданные с помощью Azure Dev Spaces, останутся вместе с рабочими нагрузками, но новые рабочие нагрузки в этих пространствах имен не будут сотвореться с пространствами Azure Dev Spaces. Кроме того, при перезапуске любых существующих стручков, оснащенных Пространствами Azure Dev, можно увидеть ошибки. Эти стручки должны быть передислоцированы без инструментария Azure Dev Spaces. Чтобы полностью удалить пробелы Azure Dev из кластера, удалите все стручки во всех пространствах имен, где было включено пространство Azure Dev Spaces.

## <a name="enable-or-remove-azure-dev-spaces-using-the-azure-portal"></a>Включить или удалить пробелы Azure Dev с помощью портала Azure

Прежде чем включить Dev Spaces с помощью портала Azure, необходимо:
* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись][az-portal-create-account].
* [Кластер AKS][create-aks-portal] в [поддерживаемом регионе.][supported-regions]

Чтобы включить пространства Azure Dev с помощью портала Azure:
1. Войдите на [портал Azure][az-portal].
1. Перейдите к кластеру AKS.
1. Выберите элемент меню *Dev Spaces.*
1. Под надписью *Enable Dev Spaces* (Включить Dev Spaces) нажмите *Да* и щелкните *Сохранить*.

![Включение Dev Spaces на портале Azure](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Включение Azure Dev Spaces с помощью портала Azure **не устанавливает** никаких инструментов для обслуживания клиентов для Azure Dev Spaces.

Чтобы удалить пробелы Azure Dev из кластера AKS, измените *пробелы включить Dev* *пробелы* в No и нажмите *Кнопка «Сохранить».* Любые пространства имен, созданные с помощью Azure Dev Spaces, останутся вместе с рабочими нагрузками, но новые рабочие нагрузки в этих пространствах имен не будут сотвореться с пространствами Azure Dev Spaces. Кроме того, при перезапуске любых существующих стручков, оснащенных Пространствами Azure Dev, можно увидеть ошибки. Эти стручки должны быть передислоцированы без инструментария Azure Dev Spaces. Чтобы полностью удалить пробелы Azure Dev из кластера, удалите все стручки во всех пространствах имен, где было включено пространство Azure Dev Spaces.

## <a name="install-the-client-side-tools"></a>Установка инструментов на стороне клиента

Клиентские инструменты Azure Dev Spaces можно использовать для взаимодействия с пробелами разработчиков в кластере AKS из локальной машины. Существует несколько способов установки инструментов со стороны клиента:

* В [Visual Studio Code][vscode]установите расширение [Azure Dev Spaces.][vscode-extension]
* В [Visual Studio 2019][visual-studio]установите рабочую нагрузку Azure Development.
* В Visual Studio 2017, установить веб-разработки рабочей нагрузки и [визуальные инструменты студии для Kubernetes][visual-studio-k8s-tools].
* Скачать и установить [Windows][cli-win], [Mac][cli-mac], или [Linux][cli-linux] CLI.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как в Azure Dev Spaces можно разрабатывать более сложные приложения в нескольких контейнерах и как упростить совместную разработку, используя разные версии и ветви кода в разных средах.

> [!div class="nextstepaction"]
> [Краткое руководство. Коллективная разработка в Kubernetes с использованием Azure Dev Spaces][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
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
