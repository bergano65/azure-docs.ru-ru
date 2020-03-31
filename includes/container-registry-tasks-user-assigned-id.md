---
title: включить файл
description: включить файл
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: ceda7bd6bd165df1eece555c6ce8a9a6c863b2c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77112353"
---
### <a name="create-a-user-assigned-identity"></a>Создание назначаемого пользователем удостоверения

Создайте идентификацию под названием *myACRTasksId* в подписке с помощью команды [создания идентификатора az.][az-identity-create] Можно использовать ту же группу ресурсов, что и ранее для создания реестра контейнеров, или другую.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Чтобы настроить удостоверение, назначенное пользователем в следующих шагах, используйте команду [шоу-идентификации аз][az-identity-show] для хранения идентификатора ресурсов, основного идентификатора и идентификатора клиента в переменных.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show