---
title: Создание кластера Kubernetes, поддерживающего Azure Dev Spaces, с помощью Azure Cloud Shell | Документация Майкрософт
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 10/04/2018
ms.topic: article
description: Узнайте, как быстро создать кластер Kubernetes, поддерживающий Azure Dev Spaces, напрямую из браузера без какой-либо установки.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: f10a84a602ce152d5c428525aa50f678b50c8b41
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48872562"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Создание кластера Kubernetes с помощью Azure Cloud Shell

Можно использовать [Azure Cloud Shell](/azure/cloud-shell) для создания кластера для Azure Dev Spaces с помощью кнопки **Попробовать** на этой странице. Если вы не выполнили вход, следуйте инструкциям, чтобы войти с помощью учетной записи Azure, а затем введите команды в командной строке Azure Cloud Shell, когда она появится.

## <a name="create-the-cluster"></a>Создание кластера

Сначала создайте группу ресурсов. Используйте один из поддерживаемых регионов (EastUS, CentralUS, WestUS2, WestEurope, CanadaCentral или CanadaEast).

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Чтобы создать кластер Kubernetes, выполните следующую команду:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.11.2 --enable-addons http_application_routing
```

Создание кластера занимает несколько минут.  По завершении выходные данные отображаются в формате JSON. Найдите `provisioningState` и убедитесь, что значение равно `Succeeded`.

## <a name="next-steps"></a>Дополнительная информация

Ссылки на полные руководства см. в разделе [Рабочие среды Azure для разработчиков](/azure/dev-spaces/).