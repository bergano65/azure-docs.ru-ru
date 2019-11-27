---
title: Управление обслуживанием для виртуальных машин Azure
description: Узнайте, как управлять применением обслуживания к виртуальным машинам Azure с помощью управления обслуживанием.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: f336bd0e208e847dbb24123285066330d8a1ce40
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535851"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>Предварительная версия: Управление обновлениями с помощью управления обслуживанием и Azure CLI

Управление обновлениями платформы, которые не нуждаются в перезагрузке, с помощью управления обслуживанием. Azure часто обновляет свою инфраструктуру для повышения надежности, производительности, безопасности или запуска новых функций. Большинство обновлений прозрачны для пользователей. Некоторые конфиденциальные рабочие нагрузки, например игры, потоковая передача мультимедиа и финансовые транзакции, не допускают даже несколько секунд заморозки или отключения виртуальной машины для обслуживания. Управление обслуживанием дает возможность ожидать обновления платформы и применять их в течение интервала в течение 35 дней. 

Управление обслуживанием позволяет решить, когда следует применять обновления к изолированным виртуальным машинам и выделенным узлам Azure.

С помощью управления обслуживанием можно:
- Пакетных обновлений в одном пакете обновления.
- Подождите 35 дней, чтобы применить обновления. 
- Автоматизируйте обновление платформы для окна обслуживания с помощью функций Azure.
- Конфигурации обслуживания работают в подписках и группах ресурсов. 

> [!IMPORTANT]
> Управление обслуживанием в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## <a name="limitations"></a>Ограничения

- Виртуальные машины должны находиться на [выделенном узле](./linux/dedicated-hosts.md)или быть созданы с использованием [ИЗОЛИРОВАННОГО размера виртуальной машины](./linux/isolation.md).
- Через 35 дней будет автоматически применено обновление.
- Пользователь должен иметь доступ **владельца ресурса** .


## <a name="install-the-maintenance-extension"></a>Установка расширения обслуживания

Если вы решили установить [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) локально, потребуется версия 2.0.76 или более поздняя.

Установите расширение CLI предварительной версии `maintenance` локально или в Cloud Shell. 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>Создание конфигурации обслуживания

Для создания конфигурации обслуживания используйте `az maintenance configuration create`. В этом примере создается конфигурация обслуживания с именем *myConfig* , областью действия которой является узел. 

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

Скопируйте идентификатор конфигурации из выходных данных для последующего использования.

Использование `--maintenanceScope host` гарантирует, что конфигурация обслуживания будет использоваться для управления обновлениями узла.

При попытке создать конфигурацию с тем же именем, но в другом расположении возникнет ошибка. Имена конфигураций должны быть уникальными для вашей подписки.

Вы можете запросить доступные конфигурации обслуживания с помощью `az maintenance configuration list`.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>Назначение конфигурации

Используйте `az maintenance assignment create`, чтобы назначить конфигурацию изолированной виртуальной машине или выделенному узлу Azure.

### <a name="isolated-vm"></a>Изолированная виртуальная машина

Примените конфигурацию к виртуальной машине, используя идентификатор конфигурации. Укажите `--resource-type virtualMachines` и укажите имя виртуальной машины для `--resource-name`, а также группу ресурсов для виртуальной машины в `--resource-group`и расположение виртуальной машины для `--location`. 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id '/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig'
```

### <a name="dedicated-host"></a>Выделенный узел

Чтобы применить конфигурацию к выделенному узлу, необходимо включить `--resource-type hosts`, `--resource-parent-name` с именем группы узлов и `--resource-parent-type hostGroups`. 

Параметр `--resource-id` является ИДЕНТИФИКАТОРом узла. Чтобы получить идентификатор выделенного узла, можно использовать команду [AZ VM Host Get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view) .

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

Можно убедиться, что конфигурация применена правильно, или проверить, какая конфигурация применяется в данный момент с помощью `az maintenance assignment list`.

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

Используйте `az maintenance update list`, чтобы узнать, есть ли ожидающие обновления. Обновление — подписка на идентификатор подписки, содержащей виртуальную машину.

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

## <a name="apply-updates"></a>Установка обновлений

Используйте `az maintenance apply update` для применения ожидающих обновлений.

### <a name="isolated-vm"></a>Изолированная виртуальная машина

Создайте запрос на применение обновлений к изолированной виртуальной машине.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myMaintenanceRG\
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Выделенный узел

Применить обновления к выделенному узлу.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>Проверка состояния применения обновлений 

Ход выполнения обновлений можно проверить с помощью `az maintenance applyupdate get`. 

### <a name="isolated-vm"></a>Изолированная виртуальная машина

Замените `myUpdateName` именем обновления, которое было возвращено при выполнении `az maintenance applyupdate create`.

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name myUpdateName 
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
   --apply-update-name default \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Удаление конфигурации обслуживания

Для удаления конфигурации обслуживания используйте `az maintenance configuration delete`. При удалении конфигурации управление обслуживанием удаляется из связанных ресурсов.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения см. в разделе [обслуживание и обновления](maintenance-and-updates.md).