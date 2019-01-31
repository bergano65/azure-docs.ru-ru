---
title: Создание кластера Kubernetes, поддерживающего Azure Dev Spaces, с помощью Azure Cloud Shell | Документация Майкрософт
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: article
description: Узнайте, как быстро создать кластер Kubernetes, поддерживающий Azure Dev Spaces, напрямую из браузера без какой-либо установки.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: d806607eb3e46d0bd04deb18756021adec59601d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466580"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Создание кластера Kubernetes с помощью Azure Cloud Shell

Можно использовать [Azure Cloud Shell](/azure/cloud-shell) для создания кластера для Azure Dev Spaces с помощью кнопки **Попробовать** на этой странице. Если вы не выполнили вход, следуйте инструкциям, чтобы войти с помощью учетной записи Azure, а затем введите команды в командной строке Azure Cloud Shell, когда она появится.

## <a name="create-the-cluster"></a>Создание кластера

Сначала создайте группу ресурсов. Используйте один из поддерживаемых регионов: EastUS (восточная часть США), EastUS2 (восточная часть США 2), CentralUS (центральная часть США), WestUS2 (западная часть США 2), WestEurope (Западная Европа), SoutheastAsia (Юго-Восточная Азия), CanadaCentral (Центральная Канада) или CanadaEast (Восточная Канада).

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Чтобы создать кластер Kubernetes, выполните следующую команду:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --kubernetes-version 1.10.9 --enable-addons http_application_routing
```

Создание кластера занимает несколько минут.  По завершении выходные данные отображаются в формате JSON. Найдите `provisioningState` и убедитесь, что значение равно `Succeeded`.

## <a name="next-steps"></a>Дополнительная информация

Ссылки на полные руководства см. в разделе [Рабочие среды Azure для разработчиков](/azure/dev-spaces/).
