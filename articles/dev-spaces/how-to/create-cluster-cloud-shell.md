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
ms.openlocfilehash: cd0c8c3c26feefe3448ada1cf1575706cd17e525
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808692"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Создание кластера Kubernetes с помощью Azure Cloud Shell

Можно использовать [Azure Cloud Shell](/azure/cloud-shell) для создания кластера службы Azure Kubernetes с помощью **попробовать** кнопки на этой странице. Если вы не выполнили вход, следуйте инструкциям, чтобы войти с помощью учетной записи Azure, а затем введите команды в командной строке Azure Cloud Shell, когда она появится.

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

> [!IMPORTANT]
> Пробелы разработки Azure краткие руководства и учебники используют пробелы разработки Azure CLI для выполнения операций. Невозможно установить Azure CLI пробелы разработки в Azure Cloud Shell.


[supported-regions]: ../about.md#supported-regions-and-configurations