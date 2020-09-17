---
author: baanders
description: Содержит файл для удаления основного экземпляра Azure Digital Twins и регистрации приложения.
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: e8f167555434338416381aaf8978a3d551686fb2
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90606601"
---
Если ресурсы, созданные для этого учебника, вам больше не нужны, можете удалить их.

В [Azure Cloud Shell](https://shell.azure.com) можно удалить все ресурсы Azure в группе ресурсов с помощью команды [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete). При этом удаляется группа ресурсов и экземпляр Azure Digital Twins.

> [!IMPORTANT]
> Удаление группы ресурсов — процесс необратимый. Группа ресурсов и все содержащиеся в ней ресурсы удаляются без возможности восстановления. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы. 

Откройте Azure Cloud Shell и выполните следующую команду, чтобы удалить группу ресурсов и все ее содержимое.

```azurecli
az group delete --name <your-resource-group>
```

Затем удалите регистрацию приложения Azure Active Directory, которую вы создали для своего клиентского приложения, с помощью следующей команды:

```azurecli
az ad app delete --id <your-application-ID>
```