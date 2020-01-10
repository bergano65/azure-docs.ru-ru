---
title: Создание кластера Kubernetes с включенной Azure Dev Spaces Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Узнайте, как быстро создать кластер Kubernetes, поддерживающий Azure Dev Spaces, напрямую из браузера без какой-либо установки.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: 35ea52b940c74b34817b4ed12d224de83f4dda74
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771110"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Создание кластера Kubernetes с включенной Azure Dev Spaces с Azure Cloud Shell

Вы можете использовать [Azure Cloud Shell](/azure/cloud-shell) для создания кластера службы Azure Kubernetes, нажав кнопку " **попробовать** " на этой странице. Если вы не выполнили вход, следуйте инструкциям, чтобы войти с помощью учетной записи Azure, а затем введите команды в командной строке Azure Cloud Shell, когда она появится.

## <a name="create-the-cluster"></a>Создайте кластер.

Сначала создайте группу ресурсов в [регионе, который поддерживает Azure dev Spaces][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Чтобы создать кластер Kubernetes, выполните следующую команду:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

Создание кластера занимает несколько минут.  По завершении выходные данные отображаются в формате JSON. Найдите `provisioningState` и убедитесь, что значение равно `Succeeded`.

## <a name="next-steps"></a>Дальнейшие действия

Ссылки на полные руководства см. в разделе [Рабочие среды Azure для разработчиков](/azure/dev-spaces/).

> [!IMPORTANT]
> Многие из кратких руководств Azure Dev Spaces и учебников используют Azure Dev Spaces CLI для выполнения операций. Интерфейс командной строки Azure Dev Spaces невозможно установить в Azure Cloud Shell.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service