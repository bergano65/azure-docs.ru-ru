---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: ca576290ea511dc54b89ecebef72ca2a42e9169f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329543"
---
### <a name="query-the-storage-queue"></a>Запрос в очередь хранилища

Команду [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) можно использовать для просмотра очередей хранилища в учетной записи, как показано в следующем примере.

```azurecli-interactive
az storage queue list --output tsv
```

Выходные данные, полученные после выполнения этой команды, включают в себя очередь `outqueue`, содранную при выполнении функции.

Затем для просмотра сообщения в очереди используйте команду [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek), как показано в следующем примере.

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Возвращаемая строка должна совпадать с сообщением, которое было отправлено для проверки функции.

> [!NOTE]  
> В предыдущем примере приведена декодированная строка, возвращенная из base64. Это происходит потому, что запись и чтение привязок хранилища очередей выполняется в хранилище Azure в качестве [строк base64](../articles/azure-functions/functions-bindings-storage-queue.md#encoding).