---
title: Получение уведомлений об обслуживании с помощью интерфейса командной строки
description: Просмотрите уведомления об обслуживании для виртуальных машин, работающих в Azure, и запустите самостоятельное обслуживание с помощью Azure CLI.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 633708219adaba2fb4c4889754b2112fbf3c4180
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069352"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Обработка уведомлений о плановом обслуживании с помощью Azure CLI

**Эта статья относится к виртуальным машинам под управлением Linux и Windows.**

С помощью интерфейса командной строки можно узнать, когда запланировано [обслуживание](maintenance-notifications.md)виртуальных машин. Сведения о плановом обслуживании доступны [на](/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view)
 
Сведения об обслуживании возвращаются, только если имеется запланированное обслуживание. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Запуск обслуживания

Следующий вызов начнет обслуживание на виртуальной машине, если для параметра задано значение `IsCustomerInitiatedMaintenanceAllowed` true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Классические развертывания

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

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
