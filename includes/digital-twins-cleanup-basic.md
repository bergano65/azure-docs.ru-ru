---
author: baanders
description: Содержит файл для удаления основного экземпляра Azure Digital Twins.
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: ab179c5f0c56a7d6fd2ebd41e13d27a832512b54
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358092"
---
Если ресурсы, созданные для этого учебника, вам больше не нужны, можете удалить их.

В [Azure Cloud Shell](https://shell.azure.com) можно удалить все ресурсы Azure в группе ресурсов с помощью команды [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete). Эта команда удаляет группу ресурсов и экземпляр Azure Digital Twins.

> [!IMPORTANT]
> Удаление группы ресурсов — процесс необратимый. Группа ресурсов и все содержащиеся в ней ресурсы удаляются без возможности восстановления. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы.

Откройте Azure Cloud Shell и выполните следующую команду, чтобы удалить группу ресурсов и все ее содержимое.

```azurecli-interactive
az group delete --name <your-resource-group>
```