---
title: Как создать кластер Kubernetes, поддержка пространствах разработки Azure, с помощью Azure Cloud Shell
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: conceptual
description: Узнайте, как быстро создать кластер Kubernetes, поддерживающий Azure Dev Spaces, напрямую из браузера без какой-либо установки.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: 532a094f96817f3c525384694b6fa60a292aa948
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391659"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Создание кластера Kubernetes с помощью Azure Cloud Shell

Можно использовать [Azure Cloud Shell](/azure/cloud-shell) для создания кластера для Azure Dev Spaces с помощью кнопки **Попробовать** на этой странице. Если вы не выполнили вход, следуйте инструкциям, чтобы войти с помощью учетной записи Azure, а затем введите команды в командной строке Azure Cloud Shell, когда она появится.

## <a name="create-the-cluster"></a>Создание кластера

Во-первых, создайте группу ресурсов в [регион, который поддерживает Azure Dev пробелы][supported-regions].

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


[supported-regions]: ../about.md#supported-regions-and-configurations