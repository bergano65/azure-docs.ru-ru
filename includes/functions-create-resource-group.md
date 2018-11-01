---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: d44865dc3189a7f9dc05106baf9f4d120e5e8bf6
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133029"
---
## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). Группа ресурсов Azure — это логический контейнер, в котором происходит развертывание ресурсов Azure (приложений-функций, баз данных и учетных записей хранения) и управление ими.

В следующем примере создается группа ресурсов `myResourceGroup`.  
Если вы не используете Cloud Shell, сначала выполните вход с помощью `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Обычно группа ресурсов и ресурсы создаются в ближайших регионах. Чтобы просмотреть все поддерживаемые расположения для планов службы приложений, выполните команду [az appservice list-locations](/cli/azure/appservice#az-appservice-list-locations).