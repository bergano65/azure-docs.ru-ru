---
title: Включение Azure Dev Spaces в AKS & Установка клиентских средств
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Узнайте, как включить Azure Dev Spaces в кластере AKS и установить клиентские средства.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: a6b3be5ceba5e60b99b2f75e060f3321cd3151f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78898951"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Включение Azure Dev Spaces в кластере AKS и установка клиентских средств

В этой статье показано несколько способов включения Azure Dev Spaces в кластере AKS, а также установки клиентских средств.

## <a name="enable-or-remove-azure-dev-spaces-using-the-cli"></a>Включение и удаление Azure Dev Spaces с помощью интерфейса командной строки

Прежде чем можно будет включить модули разработки с помощью интерфейса командной строки, вам потребуется:
* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись][az-portal-create-account].
* [Azure CLI установлен][install-cli].
* [Кластер AKS][create-aks-cli] в [поддерживаемом регионе][supported-regions].

С помощью команды `use-dev-spaces` включите Dev Spaces в кластере AKS и следуйте инструкциям на экране.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Приведенная выше команда включает пространства разработки в кластере *myAKSCluster* в группе *myResourceGroup* и создает пространство разработки *по умолчанию* .

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

`use-dev-spaces` Команда также устанавливает интерфейс командной строки Azure dev Spaces.

Чтобы удалить Azure Dev Spaces из кластера AKS, используйте `azds remove` команду. Пример:

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Приведенная выше команда удаляет Azure Dev Spaces из кластера *мякс* в *MyResourceGroup*. Все пространства имен, созданные с помощью Azure Dev Spaces, будут сохраняться вместе с рабочими нагрузками, но новые рабочие нагрузки в этих пространствах имен не будут инструментированы с помощью Azure Dev Spaces. Кроме того, при перезапуске всех существующих модулей Pod, оснащенных Azure Dev Spaces, могут возникнуть ошибки. Эти модули необходимо повторно развернуть без Azure Dev Spaces инструментов. Чтобы полностью удалить Azure Dev Spaces из кластера, удалите все модули Pod во всех пространствах имен, где включено Azure Dev Spaces.

## <a name="enable-or-remove-azure-dev-spaces-using-the-azure-portal"></a>Включение и удаление Azure Dev Spaces с помощью портал Azure

Прежде чем можно будет включить модули разработки с помощью портал Azure, вам потребуется:
* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись][az-portal-create-account].
* [Кластер AKS][create-aks-portal] в [поддерживаемом регионе][supported-regions].

Чтобы включить Azure Dev Spaces с помощью портал Azure:
1. Войдите на [портал Azure][az-portal].
1. Перейдите к кластеру AKS.
1. Выберите пункт меню *пространства разработки* .
1. Под надписью *Enable Dev Spaces* (Включить Dev Spaces) нажмите *Да* и щелкните *Сохранить*.

![Включение Dev Spaces на портале Azure](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

Включение Azure Dev Spaces с **помощью портал Azure не** устанавливает клиентские средства для Azure dev Spaces.

Чтобы удалить Azure Dev Spaces из кластера AKS, измените *параметр включить пространства разработки* в значение *нет* и нажмите кнопку *сохранить*. Все пространства имен, созданные с помощью Azure Dev Spaces, будут сохраняться вместе с рабочими нагрузками, но новые рабочие нагрузки в этих пространствах имен не будут инструментированы с помощью Azure Dev Spaces. Кроме того, при перезапуске всех существующих модулей Pod, оснащенных Azure Dev Spaces, могут возникнуть ошибки. Эти модули необходимо повторно развернуть без Azure Dev Spaces инструментов. Чтобы полностью удалить Azure Dev Spaces из кластера, удалите все модули Pod во всех пространствах имен, где включено Azure Dev Spaces.

## <a name="install-the-client-side-tools"></a>Установка клиентских средств

Для взаимодействия с пространствами разработки в кластере AKS с локального компьютера можно использовать клиентские средства Azure Dev Spaces. Существует несколько способов установки клиентских средств:

* В [Visual Studio Code][vscode]установите [расширение Azure dev Spaces][vscode-extension].
* В [Visual Studio 2019][visual-studio]установите рабочую нагрузку разработка для Azure.
* В Visual Studio 2017 установите рабочую нагрузку веб-разработки и [средства Visual Studio для Kubernetes][visual-studio-k8s-tools].
* Скачайте и установите интерфейс командной строки [Windows][cli-win], [Mac][cli-mac]или [Linux][cli-linux] .

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
