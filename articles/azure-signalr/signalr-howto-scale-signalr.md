---
title: Масштабирование экземпляра службы сигнальных сигналов Azure
description: Узнайте, как масштабировать экземпляр службы Azure SignalR для добавления или уменьшения емкости через портал Azure или Azure CLI.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: zhshang
ms.openlocfilehash: c8d74342e624b837c7ee803a2bcdcc12a3fb814b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659293"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Как масштабировать экземпляр службы СигналR Azure?
В этой статье показано, как масштабировать экземпляр службы Azure SignalR. Существует два сценария масштабирования, масштабирования и масштабирования.

* [Масштабирование:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)Получить больше единиц, соединения, сообщения и многое другое. Масштабируйте масштабирование, изменяя уровень ценообразования с бесплатного на стандартный.
* [Масштабируйте:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)Увеличьте количество единиц SignalR. Вы можете масштабировать до 100 единиц.

Настройки шкалы занимают несколько минут. В редких случаях, это может занять около 30 минут, чтобы применить. Они не требуют, чтобы вы изменили код или передислоцировали серверное приложение.

Подробную информацию о ценах и мощностях индивидуальной службы SignalR можно узнать на примере [сервиса Azure SignalR.](https://azure.microsoft.com/pricing/details/signalr-service/)  

> [!NOTE]
> Изменение службы SignalR от **бесплатного** уровня до **стандартного** уровня или наоборот, IP-адрес государственной службы будет изменен, и обычно для распространения изменений на DNS-серверах по всему интернету требуется 30-60 минут. Ваша служба может быть недосягаемой до обновления DNS. Как правило, не рекомендуется менять свой уровень ценообразования слишком часто.


## <a name="scale-on-azure-portal"></a>Масштаб на портале Azure

1. В браузере откройте [портал Azure.](https://portal.azure.com)

2. На странице службы SignalR из левого меню выберите **шкалу.**
   
3. Выберите свой ценовой уровень, а затем нажмите **Выберите**. Установите значение единицы для **стандартного** уровня.
   
    ![Масштаб на портале](./media/signalr-howto-scale/signalr-howto-scale.png)

4. Нажмите **Сохранить**.

## <a name="scale-using-azure-cli"></a>Масштабирование с помощью интерфейса командной строки Azure

Этот скрипт создает новый ресурс службы SignalR **Free** Tier и новую группу ресурсов и масштабирует его до **стандартного** уровня. 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

Запишите фактическое имя, созданное для новой группы ресурсов. Это имя группы ресурсов вам понадобится для удаления групп ресурсов.

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>Сравнение ценовых категорий

Подробная информация, например, включенные сообщения [SignalR Service Pricing Details](https://azure.microsoft.com/pricing/details/signalr-service/)и соединения для каждого ценового уровня, см.

Для таблицы ограничений обслуживания, квот и ограничений [SignalR Service limits](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits)на каждом уровне см.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали о том, как масштабировать один экземпляр службы SignalR.

Несколько конечных точек также поддерживаются для масштабирования, осколков и кросс-региона.

> [!div class="nextstepaction"]
> [Масштабная служба SignalR с несколькими экземплярами](./signalr-howto-scale-multi-instances.md)
