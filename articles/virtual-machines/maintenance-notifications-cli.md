---
title: Получение уведомлений об обслуживании для виртуальных машин Azure с помощью интерфейса командной строки
description: Просмотрите уведомления об обслуживании для виртуальных машин, работающих в Azure, и запустите самостоятельное обслуживание с помощью Azure CLI.
services: virtual-machines
author: shants123
tags: azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 3c75adc2bf5974c1bce9f05306342068396ae62b
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535864"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Обработка уведомлений о плановом обслуживании с помощью Azure CLI

**Эта статья относится к виртуальным машинам под управлением Linux и Windows.**

С помощью интерфейса командной строки можно узнать, когда запланировано [обслуживание](maintenance-notifications.md)виртуальных машин. Сведения о плановом обслуживании доступны [на](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view)
 
Сведения об обслуживании возвращаются, только если имеется запланированное обслуживание. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Запуск обслуживания

Следующий вызов начнет обслуживание на виртуальной машине, если `IsCustomerInitiatedMaintenanceAllowed` имеет значение true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Классические развертывания


Если у вас все еще есть устаревшие виртуальные машины, развернутые с использованием классической модели развертывания, вы можете отправить к ним запрос и запустить их обслуживание с помощью Azure CLI.

Чтобы убедиться, находитесь ли вы в правильном режиме для работы с классической виртуальной машиной, введите:

```
azure config mode asm
```

Чтобы узнать состояние обслуживания виртуальной машины с именем *myVM*, введите:

```
azure vm show myVM 
``` 

Чтобы начать обслуживание классической виртуальной машины с именем *myVM* в службе *myService* и развертывании *myDeployment*, введите команду:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Дальнейшие действия

Вы также можете управлять плановым обслуживанием с помощью [Azure PowerShell](maintenance-notifications-powershell.md) или [портала](maintenance-notifications-portal.md).
