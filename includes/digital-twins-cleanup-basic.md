---
author: baanders
description: Содержит файл для удаления основного экземпляра Azure Digital Twins.
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 4c03ef942896dda63f678018cdd257024cfbb6d4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011293"
---
Если ресурсы, созданные для этого учебника, вам больше не нужны, можете удалить их.

В [Azure Cloud Shell](https://shell.azure.com) можно удалить все ресурсы Azure в группе ресурсов с помощью команды [az group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete). Эта команда удаляет группу ресурсов и экземпляр Azure Digital Twins.

> [!IMPORTANT]
> Удаление группы ресурсов — процесс необратимый. Группа ресурсов и все содержащиеся в ней ресурсы удаляются без возможности восстановления. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы.

Откройте Azure Cloud Shell и выполните следующую команду, чтобы удалить группу ресурсов и все ее содержимое.

```azurecli-interactive
az group delete --name <your-resource-group>
```