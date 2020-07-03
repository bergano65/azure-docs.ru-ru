---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77474283"
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
> В предыдущем примере приведена декодированная строка, возвращенная из base64. Это происходит потому, что запись и чтение привязок хранилища очередей выполняется в хранилище Azure в качестве [строк base64](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding).