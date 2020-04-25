---
title: Управление обслуживанием виртуальных машин Azure с помощью интерфейса командной строки
description: Узнайте, как управлять применением обслуживания к виртуальным машинам Azure с помощью управления обслуживанием и интерфейса командной строки.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/20/2020
ms.author: cynthn
ms.openlocfilehash: 4843b4769e31748fd5f624005792c604db18f11e
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137507"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>Управление обновлениями с помощью управления обслуживанием и Azure CLI

Управление обслуживанием позволяет решить, когда следует применять обновления к изолированным виртуальным машинам и выделенным узлам Azure. В этом разделе рассматриваются параметры Azure CLI для управления обслуживанием. Дополнительные сведения о преимуществах использования управления обслуживанием, его ограничений и других параметров управления см. [в разделе Управление обновлениями платформы с помощью управления обслуживанием](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Создание конфигурации обслуживания

Используйте `az maintenance configuration create` для создания конфигурации обслуживания. В этом примере создается конфигурация обслуживания с именем *myConfig* , областью действия которой является узел. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location eastus
```

Скопируйте идентификатор конфигурации из выходных данных для последующего использования.

Использование `--maintenanceScope host` гарантирует, что конфигурация обслуживания будет использоваться для управления обновлениями узла.

При попытке создать конфигурацию с тем же именем, но в другом расположении возникнет ошибка. Имена конфигураций должны быть уникальными для вашей подписки.

Можно запросить доступные конфигурации обслуживания с помощью `az maintenance configuration list`.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>Назначение конфигурации

Используйте `az maintenance assignment create` для назначения конфигурации изолированной виртуальной машине или выделенному узлу Azure.

### <a name="isolated-vm"></a>Изолированная виртуальная машина

Примените конфигурацию к виртуальной машине, используя идентификатор конфигурации. Укажите `--resource-type virtualMachines` и введите имя виртуальной машины для `--resource-name`, а также группу ресурсов для виртуальной машины в `--resource-group`и расположение виртуальной машины для. `--location` 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>Выделенный узел

Чтобы применить конфигурацию к выделенному узлу, необходимо включить `--resource-type hosts`, `--resource-parent-name` с именем группы узлов и. `--resource-parent-type hostGroups` 

Параметр `--resource-id` — это идентификатор узла. Чтобы получить идентификатор выделенного узла, можно использовать команду [AZ VM Host Get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view) .

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>Проверить конфигурацию

Можно убедиться, что конфигурация применена правильно, или проверить, какая конфигурация используется в данный момент `az maintenance assignment list`.

### <a name="isolated-vm"></a>Изолированная виртуальная машина

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>Выделенный узел 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>Проверить наличие ожидающих обновлений

Используйте `az maintenance update list` , чтобы узнать, есть ли ожидающие обновления. Обновление — подписка на идентификатор подписки, содержащей виртуальную машину.

Если обновления отсутствуют, команда возвратит сообщение об ошибке, которое будет содержать текст: `Resource not found...StatusCode: 404`.

При наличии обновлений будет возвращено только одно из них, даже если ожидается несколько обновлений. Данные для этого обновления будут возвращены в объект:

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>Изолированная виртуальная машина

Проверьте наличие ожидающих обновлений для изолированной виртуальной машины. В этом примере выходные данные форматируются в виде таблицы для удобства чтения.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Выделенный узел

Проверка наличия ожидающих обновлений для выделенного узла. В этом примере выходные данные форматируются в виде таблицы для удобства чтения. Замените значения для ресурсов собственными.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>Применение обновлений

Используйте `az maintenance apply update` для применения ожидающих обновлений. При успешном выполнении эта команда вернет JSON, содержащий сведения об обновлении.

### <a name="isolated-vm"></a>Изолированная виртуальная машина

Создайте запрос на применение обновлений к изолированной виртуальной машине.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Выделенный узел

Применить обновления к выделенному узлу.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>Проверка состояния применения обновлений 

Ход выполнения обновлений можно проверить с помощью `az maintenance applyupdate get`. 

Можно использовать `default` в качестве имени обновления, чтобы просмотреть результаты последнего обновления, или заменить `myUpdateName` именем обновления, которое было возвращено при выполнении. `az maintenance applyupdate create`

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
Ластупдатетиме будет считать время завершения обновления, инициированное вами или платформой в случае, если окно самостоятельного обслуживания не использовалось. Если обновление не было применено через контроль обслуживания, будет отображаться значение по умолчанию.

### <a name="isolated-vm"></a>Изолированная виртуальная машина

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>Выделенный узел

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Удаление конфигурации обслуживания

Используйте `az maintenance configuration delete` для удаления конфигурации обслуживания. При удалении конфигурации управление обслуживанием удаляется из связанных ресурсов.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>Следующие шаги
Дополнительные сведения см. в разделе [обслуживание и обновления](maintenance-and-updates.md).
