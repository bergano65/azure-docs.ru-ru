---
author: baanders
description: Содержит файл для удаления основного экземпляра Azure Digital Twins.
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 29a07ffa917153c0cb062d34e2807d43d039a373
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494677"
---
Если ресурсы, созданные для этого учебника, вам больше не нужны, можете удалить их.

В [Azure Cloud Shell](https://shell.azure.com) можно удалить все ресурсы Azure в группе ресурсов с помощью команды [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete). При этом удаляется группа ресурсов и экземпляр Azure Digital Twins.

> [!IMPORTANT]
> Удаление группы ресурсов — процесс необратимый. Группа ресурсов и все содержащиеся в ней ресурсы удаляются без возможности восстановления. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. 

Откройте Azure Cloud Shell и выполните следующую команду, чтобы удалить группу ресурсов и все ее содержимое.

```azurecli-interactive
az group delete --name <your-resource-group>
```