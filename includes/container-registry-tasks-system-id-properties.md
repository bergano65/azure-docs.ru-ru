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
ms.openlocfilehash: 700dbfde3be2f24eb57acbdeb9d2841ef2bdfe44
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2020
ms.locfileid: "77112298"
---
В выходных данных команды в разделе `identity` показано удостоверение типа `SystemAssigned` задается в задаче. `principalId` является ИДЕНТИФИКАТОРом субъекта для удостоверения задачи:

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 
Используйте команду [AZ запись контроля][az-acr-task-show] доступа, чтобы сохранить principalId в переменной для использования в последующих командах. Замените имя задачи и реестра в следующей команде:

```azurecli
principalID=$(az acr task show --name mytask --registry myregistry --query identity.principalId --output tsv)
```

<!-- LINKS - Internal -->
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
