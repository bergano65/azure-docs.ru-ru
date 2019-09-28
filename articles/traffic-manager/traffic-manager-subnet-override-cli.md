---
title: Переопределение подсети диспетчера трафика Azure с помощью Azure CLI | Документация Майкрософт
description: Эта статья поможет вам понять, как можно использовать переопределение подсети диспетчера трафика для переопределения метода маршрутизации профиля диспетчера трафика, чтобы направить трафик в конечную точку на основе IP-адреса конечного пользователя через предопределенный диапазон IP-адресов для сопоставления конечных точек.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: d285db22ef92128e025a677ea7f86d623dfe130c
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351179"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Переопределение подсети диспетчера трафика с помощью Azure CLI

Переопределение подсети диспетчера трафика позволяет изменить метод маршрутизации профиля.  Добавление переопределения направляет трафик, основанный на IP-адресе конечного пользователя, с предопределенным диапазоном IP-адресов для сопоставления конечных точек. 

## <a name="how-subnet-override-works"></a>Как работает переопределение подсети

Когда переопределение подсети добавляется в профиль диспетчера трафика, диспетчер трафика сначала проверяет, есть ли переопределение подсети для IP-адреса конечного пользователя. Если он найден, запрос DNS пользователя будет перенаправлен в соответствующую конечную точку.  Если сопоставление не найдено, диспетчер трафика будет возвращаться к исходному методу маршрутизации профиля. 

Диапазоны IP-адресов могут быть указаны либо в виде диапазонов CIDR (например, 1.2.3.0/24), либо в качестве диапазонов адресов (например, 1.2.3.4-5.6.7.8). Диапазоны IP-адресов, связанные с каждой конечной точкой, должны быть уникальными для этой конечной точки. Любое перекрытие диапазонов IP-адресов между разными конечными точками приведет к тому, что профиль будет отклонен диспетчером трафика.

Существуют два типа профилей маршрутизации, которые поддерживают переопределения подсетей.

* **География** . Если диспетчер трафика находит переопределение подсети для IP-адреса DNS-запроса, он направляет запрос в конечную точку, независимо от работоспособности конечной точки.
* **Производительность** . Если диспетчер трафика находит переопределение подсети для IP-адреса DNS-запроса, он будет направлять трафик в конечную точку только в том случае, если он исправен.  Диспетчер трафика будет передаваться на эвристику маршрутизации производительности, если конечная точка переопределения подсети находится в неработоспособном состоянии.

## <a name="create-a-traffic-manager-subnet-override"></a>Создание переопределения подсети диспетчера трафика

Чтобы создать переопределение подсети диспетчера трафика, можно использовать Azure CLI, чтобы добавить подсети для переопределения в конечную точку диспетчера трафика.

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0.28 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Обновите конечную точку диспетчера трафика с переопределением подсети.
Используйте Azure CLI для обновления конечной точки с помощью команды [AZ Network трафика — обновление конечной точки диспетчера](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

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

Диапазоны IP-адресов можно удалить, выполнив [Обновление конечной точки AZ Network Configuration Manager](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) с параметром **--Remove** .

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>Следующие шаги
См. дополнительные сведения в статье [Методы маршрутизации трафика диспетчером трафика](traffic-manager-routing-methods.md).

Сведения о [методе маршрутизации трафика подсети](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)