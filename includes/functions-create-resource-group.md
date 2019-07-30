---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: a2994602f857c2c8ff9f935b649a8d3e94c10dca
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444112"
---
## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group). Группа ресурсов Azure — это логический контейнер, в котором происходит развертывание ресурсов Azure (приложений-функций, баз данных и учетных записей хранения) и управление ими.

В следующем примере создается группа ресурсов `myResourceGroup`.  
Если вы не используете Cloud Shell, сначала выполните вход с помощью `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Группу ресурсов и ресурсы целесообразно создавать в ближайшем к вам [регионе](https://azure.microsoft.com/global-infrastructure/regions/). 
