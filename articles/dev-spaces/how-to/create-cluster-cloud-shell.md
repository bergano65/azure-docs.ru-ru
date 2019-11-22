---
title: Создание кластера Kubernetes, включенного для Azure Dev Spaces с помощью Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Узнайте, как быстро создать кластер Kubernetes, поддерживающий Azure Dev Spaces, напрямую из браузера без какой-либо установки.
keywords: Docker, Kubernetes, Azure, служба контейнеров Azure, служба Azure Kubernetes, контейнеры, Helm, сетка службы, сетка службы маршрутизации, kubectl, k8s
ms.openlocfilehash: dbdc9226e417b3142284386ae3586819cda802d9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280111"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Создание кластера Kubernetes с помощью Azure Cloud Shell

Вы можете использовать [Azure Cloud Shell](/azure/cloud-shell) для создания кластера службы Azure Kubernetes, нажав кнопку " **попробовать** " на этой странице. Если вы не выполнили вход, следуйте инструкциям, чтобы войти с помощью учетной записи Azure, а затем введите команды в командной строке Azure Cloud Shell, когда она появится.

## <a name="create-the-cluster"></a>Создание кластера

Сначала создайте группу ресурсов в [регионе, который поддерживает Azure dev Spaces][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Чтобы создать кластер Kubernetes, выполните следующую команду:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

Создание кластера занимает несколько минут.  По завершении выходные данные отображаются в формате JSON. Найдите `provisioningState` и убедитесь, что значение равно `Succeeded`.

## <a name="next-steps"></a>Дополнительная информация

Ссылки на полные руководства см. в разделе [Рабочие среды Azure для разработчиков](/azure/dev-spaces/).

> [!IMPORTANT]
> Многие из кратких руководств Azure Dev Spaces и учебников используют Azure Dev Spaces CLI для выполнения операций. Интерфейс командной строки Azure Dev Spaces невозможно установить в Azure Cloud Shell.


[supported-regions]: ../about.md#supported-regions-and-configurations