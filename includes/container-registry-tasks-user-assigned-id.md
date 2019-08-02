---
title: включение файла
description: включение файла
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0e2acb346fad87e0c1c7fd7de1389d8fc86206d0
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642115"
---
### <a name="create-a-user-assigned-identity"></a>Создание назначаемого пользователем удостоверения

Создайте удостоверение с именем *мякртасксид* в подписке с помощью команды [AZ Identity Create][az-identity-create] . Вы можете использовать ту же группу ресурсов, которая использовалась ранее для создания реестра контейнеров или другого.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Чтобы настроить определяемое пользователем удостоверение в следующих шагах, используйте команду [AZ Identity показывать][az-identity-show] , чтобы сохранить идентификатор ресурса, идентификатор субъекта-службы и идентификатор клиента в переменных.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show