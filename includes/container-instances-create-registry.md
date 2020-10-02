---
title: включить файл
description: включить файл
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 2b1d9b7f9ff07a3e0c7745191decc3e82181553e
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708025"
---
## <a name="create-azure-container-registry"></a>Создание реестра контейнеров Azure

Перед созданием реестра контейнеров необходимо создать *группу ресурсов*, куда он будет развернут. Группа ресурсов — это логическая коллекция, в которой выполняется развертывание и администрирование всех ресурсов Azure.

Создайте группу ресурсов с помощью команды [az group create][az-group-create]. В следующем примере создается группа ресурсов с именем *myResourceGroup* в регионе *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

После создания группы ресурсов создайте реестр контейнеров Azure с помощью команды [az acr create][az-acr-create]. Имя реестра контейнеров должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых знаков. Замените `<acrName>` уникальным именем реестра.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Ниже приведена часть выходных данных для нового реестра контейнеров Azure *mycontainerregistry082*.

```output
{
  "creationDate": "2020-07-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

В остальной части руководства `<acrName>` используется как заполнитель для имени реестра контейнеров, выбранного на этом шаге.

## <a name="log-in-to-container-registry"></a>Вход в реестр контейнеров

Войдите в экземпляр реестра контейнеров Azure, прежде чем передавать в него образы. Используйте команду [az acr login][az-acr-login], чтобы выполнить операцию. Укажите уникальное имя реестра контейнеров, выбранное при его создании.

```azurecli
az acr login --name <acrName>
```

Пример:

```azurecli
az acr login --name mycontainerregistry082
```

По завершении команда возвращает `Login Succeeded`.

```output
Login Succeeded
```

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create