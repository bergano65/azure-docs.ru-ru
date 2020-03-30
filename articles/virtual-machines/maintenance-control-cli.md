---
title: Управление обслуживанием
description: Узнайте, как управлять, когда техническое обслуживание применяется к вашим VMs-м вмс ажиотаж с помощью управления обслуживанием.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: 58c0964d170f49066802b955f09dab01eaf998a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250183"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>Предварительный просмотр: Обновления управления с помощью управления обслуживанием и Azure CLI

Управление обновлениями платформы, которые не требуют перезагрузки, с помощью управления обслуживанием. Azure часто обновляет свою инфраструктуру для повышения надежности, производительности, безопасности или запуска новых функций. Большинство обновлений являются прозрачными для пользователей. Некоторые чувствительные рабочие нагрузки, такие как игры, потоковое воспроизведение мультимедиа и финансовые операции, не могут допустить даже нескольких секунд замораживания или отключения VM для обслуживания. Управление обслуживанием дает вам возможность ждать обновления платформы и применять их в течение 35-дневного прокатного окна. 

Управление обслуживанием позволяет решать, когда следует применять обновления для изолированных VMs и выделенных хостов Azure.

С помощью управления обслуживанием, вы можете:
- Обновления пакета в один пакет обновлений.
- Подождите до 35 дней, чтобы применить обновления. 
- Автоматируйте обновления платформы для окна обслуживания с помощью функций Azure.
- Конфигурации обслуживания работают между подписками и группами ресурсов. 

> [!IMPORTANT]
> Управление обслуживанием в настоящее время находится в общедоступном предварительном просмотре.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="limitations"></a>Ограничения

- VMs должны быть на [выделенном хосте](./linux/dedicated-hosts.md)или быть созданы с использованием [изолированного размера VM.](./linux/isolation.md)
- Через 35 дней обновление будет автоматически применено.
- Пользователь должен иметь доступ **к ресурсу.**


## <a name="install-the-maintenance-extension"></a>Установка расширения обслуживания

Если вы решили установить [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) локально, вам нужна версия 2.0.76 или позже.

Установите `maintenance` расширение предварительного просмотра CLI локально или в облачную оболочку. 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>Создание конфигурации обслуживания

Используется `az maintenance configuration create` для создания конфигурации обслуживания. Этот пример создает конфигурацию обслуживания под названием *myConfig,* прицелваемую для узла. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location  eastus
```

Копирование идентификатора конфигурации из вывода для последующего использования.

Использование `--maintenanceScope host` гарантирует, что конфигурация обслуживания используется для управления обновлениями для узла.

Если вы попытаетесь создать конфигурацию с тем же именем, но в другом месте, вы получите ошибку. Имена конфигураций должны быть уникальными для вашей подписки.

Вы можете запросить доступные `az maintenance configuration list`конфигурации обслуживания с помощью.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>Назначить конфигурацию

Используйте `az maintenance assignment create` для присвоения конфигурации изолированной VM или Azure Dedicated Host.

### <a name="isolated-vm"></a>Изолированный VM

Примените конфигурацию к VM, используя идентификатор конфигурации. Укажите `--resource-type virtualMachines` и предоставим название `--resource-name`VM для , и ресурсная `--resource-group`группа для VM в `--location`, и расположение VM для . 

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

Чтобы применить конфигурацию к выделенной хосте, необходимо включить, `--resource-type hosts` `--resource-parent-type hostGroups` `--resource-parent-name` с именем группы хоста, и. 

Параметр `--resource-id` является идентификатором хоста. Вы можете использовать [az vm хост получить-экземпляр-вид,](/cli/azure/vm/host#az-vm-host-get-instance-view) чтобы получить идентификатор вашего выделенного хоста.

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

Вы можете проверить, правильно ли была применена конфигурация, или `az maintenance assignment list`проверить, какая конфигурация в настоящее время применяется с помощью.

### <a name="isolated-vm"></a>Изолированный VM

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


## <a name="check-for-pending-updates"></a>Проверка ожидающих обновления

Используйте, `az maintenance update list` чтобы увидеть, есть ли ожидающие обновления. Обновление - подписка, чтобы быть идентификатором для подписки, которая содержит VM.

Если нет обновлений, команда вернет сообщение об ошибке, которое будет содержать текст: `Resource not found...StatusCode: 404`.

Если есть обновления, только один будет возвращен, даже если Есть несколько обновлений в ожидании. Данные для этого обновления будут возвращены в объекте:

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

### <a name="isolated-vm"></a>Изолированный VM

Проверьте ожидающие обновления для изолированного VM. В этом примере вывод отформатирован как таблица для читаемости.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Выделенный узел

Проверка ожидающих обновления для выделенного узла. В этом примере вывод отформатирован как таблица для читаемости. Замените значения для ресурсов своими собственными.

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

Используйте `az maintenance apply update` для применения ожидающих обновлений. При успехе эта команда вернет JSON, содержащий детали обновления.

### <a name="isolated-vm"></a>Изолированный VM

Создайте запрос на применение обновлений к изолированной VM.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Выделенный узел

Применяйте обновления к выделенной хосте.

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

## <a name="check-the-status-of-applying-updates"></a>Проверьте состояние применения обновлений 

Вы можете проверить ход обновления `az maintenance applyupdate get`с помощью . 

Вы можете `default` использовать в качестве имени обновления, чтобы `myUpdateName` увидеть результаты для последнего обновления, `az maintenance applyupdate create`или заменить имя обновления, которое было возвращено при выходе.

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
LastUpdateTime будет время, когда обновление получило завершение, либо инициировано вами или платформой в случае, если окно самообслуживания не было использовано. Если никогда не было обновления, применяемого с помощью управления обслуживанием, оно покажет значение по умолчанию.

### <a name="isolated-vm"></a>Изолированный VM

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


## <a name="delete-a-maintenance-configuration"></a>Удалить конфигурацию обслуживания

Используйте `az maintenance configuration delete` для удаления конфигурации обслуживания. Удаление конфигурации удаляет элемент управления обслуживания из связанных ресурсов.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>Дальнейшие действия
Чтобы узнать больше, смотрите [техническое обслуживание и обновления](maintenance-and-updates.md).
