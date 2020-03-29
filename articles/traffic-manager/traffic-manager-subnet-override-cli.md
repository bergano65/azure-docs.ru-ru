---
title: Подсеть Azure Traffic Manager переопределения с помощью Azure CLI (ru) Документы Майкрософт
description: Эта статья поможет вам понять, как переопределение подсети Traffic Manager может быть использовано для переопределения метода направления профиля менеджера трафика, чтобы направить трафик на конечную точку, основанную на IP-адресе конечного пользователя через предопределенный диапазон IP-адрес к конечным точкам отображения.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 818b692884bd9d31efd08663a582ebcfec2032e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938470"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Переопределение подсети менеджера трафика с помощью Azure CLI

Переопределение подсети Traffic Manager позволяет изменить метод реуктора профиля.  Добавление переопределения будет направлять трафик на основе IP-адреса конечного пользователя с предопределенным диапазоном IP-адреса для отображения конечной точки. 

## <a name="how-subnet-override-works"></a>Как работает переопределение подсети

Когда переопределения подсети добавляются в профиль менеджера трафика, диспетчер трафика сначала проверяет, есть ли переопределение подсети для IP-адреса конечномго пользователя. Если один из них найден, dNS-запрос пользователя будет направлен в соответствующую конечную точку.  Если отображение не найдено, диспетчер трафика вернется к исходной методу реуктора профиля. 

Диапазоны IP-адресов могут быть указаны как диапазоны CIDR (например, 1.2.3.0/24), либо как диапазоны адресов (например, 1.2.3.4-5.6.8). Диапазоны IP, связанные с каждой конечной точкой, должны быть уникальными для этой конечных точек. Любое перекрытие диапазонов IP между различными конечными точками приведет к отклонению профиля менеджером трафика.

Существует два типа профилей реанивных профилей, поддерживающих переопределения подсети:

* **Географический** - Если диспетчер трафика находит переопределение подсети для IP-адреса запроса DNS, он будет направлять запрос в конечную точку независимо от работы конечной точки.
* **Производительность** - Если диспетчер трафика находит переопределение подсети для IP-адреса запроса DNS, он будет направлять трафик только в конечную точку, если он здоров.  Диспетчер трафика вернется к эвристическому движению, если конечная точка переопределения подсети не является здоровой.

## <a name="create-a-traffic-manager-subnet-override"></a>Создание переопределения подсети менеджера трафика

Для создания переопределения подсети менеджера трафика можно использовать Azure CLI для добавления подсетей для переопределения в конечную точку диспетчера трафика.

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0.28 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить, [см.]( /cli/azure/install-azure-cli)

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Обновление конечная точка менеджера трафика с переопределением подсети.
Используйте Azure CLI для обновления своей конечной точки с [помощью обновления конечных точек](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update)менеджера сети az.

```azurecli

### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints

```

Вы можете удалить диапазоны IP-адресов, запустив [обновление конечных точек управления трафиком сети аз](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) с опцией **--удалить.**

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>Next Steps
См. дополнительные сведения в статье [Методы маршрутизации трафика диспетчером трафика](traffic-manager-routing-methods.md).

Узнайте о [методе реуктовирования трафика Subnet](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)