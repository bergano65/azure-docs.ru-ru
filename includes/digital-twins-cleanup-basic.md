---
author: baanders
description: включаемый файл для удаления экземпляра Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: c3c1b814b357a2e4b724590261657e485852f99c
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575703"
---
* **Если вам не нужны ресурсы, созданные при работе с этим руководством**, вы можете удалить их вместе с экземпляром Azure Digital Twins с помощью команды [az group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete). При этом будут удалены все ресурсы Azure в группе ресурсов, а также сама группа.
    
    > [!IMPORTANT]
    > Удаление группы ресурсов — процесс необратимый. Группа ресурсов и все содержащиеся в ней ресурсы удаляются без возможности восстановления. Будьте внимательны, чтобы случайно не удалить не ту группу ресурсов или не те ресурсы.
    
    Откройте [Azure Cloud Shell](https://shell.azure.com) и выполните приведенную ниже команду, чтобы удалить группу ресурсов и все ее содержимое.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```