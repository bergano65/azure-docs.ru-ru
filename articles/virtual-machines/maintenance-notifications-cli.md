---
title: Получайте уведомления о техническом обслуживании с помощью CLI
description: Просматривайте уведомления об обслуживании виртуальных машин, работающих в Azure, и приступайте к обслуживанию самообслуживания с помощью Azure CLI.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 4ad57c1c71a51f948bd405a5487a1e27e36bfff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920898"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Обработка запланированных уведомлений об обслуживании с помощью Azure CLI

**Эта статья распространяется на виртуальные машины под управлением Как Linux, так и Windows.**

Вы можете использовать CLI, чтобы увидеть, когда vMs запланированы на [техническое обслуживание.](maintenance-notifications.md) Информация о планируемом обслуживании доступна из [az vm get-instance-view.](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view)
 
Сведения об обслуживании возвращаются, только если имеется запланированное обслуживание. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Начало технического обслуживания

Следующий вызов начнется обслуживание на `IsCustomerInitiatedMaintenanceAllowed` VM, если будет установлен о правде.

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

Вы также можете обрабатывать запланированное техническое обслуживание с помощью [Azure PowerShell](maintenance-notifications-powershell.md) или [портала.](maintenance-notifications-portal.md)
