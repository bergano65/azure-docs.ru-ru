---
title: Экземпляров набора защиты экземпляра для масштабирования виртуальных машин Azure | Документация Майкрософт
description: Узнайте, как защитить экземпляры масштабируемого набора виртуальных машин Azure из операции свертывания и масштабируемый набор.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: manayar
ms.openlocfilehash: 61430f5a43a04fa0e5b2f0c79ff03419c73aaf28
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66416549"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances-preview"></a>Защита экземпляра для масштабирования виртуальных машин Azure набор экземпляров (Предварительная версия)
Наборы масштабирования виртуальных машин Azure включить повышения эластичности для рабочих нагрузок через [автомасштабирования](virtual-machine-scale-sets-autoscale-overview.md), поэтому можно настроить, когда масштабирование инфраструктуры, и время шкал. Масштабируемые наборы также позволяют централизованно управлять, Настройка и обновление большого количества виртуальных машин через различные [политику обновления](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) параметры. Вы можете настроить обновление в модели масштабируемого набора и новую конфигурацию автоматически применяется к экземпляру каждого масштабируемого набора при выборе политики обновления автоматически или прокрутка.

Как приложение обрабатывает трафик, может существовать экземпляр набора в тех случаях, когда конкретные экземпляры отличать от остальной части шкалы. Например некоторые экземпляры в масштабируемый набор может выполнять длительные операции и вы не хотите, эти экземпляры в можно масштабировать до завершения операции. Может также специализированные несколько экземпляров в масштабируемом наборе, для выполнения дополнительных или других задач, чем другие элементы масштабируемого набора. Требуется «дополнительно» виртуальные машины не для изменения с другими экземплярами в масштабируемом наборе. Защита экземпляра предоставляет дополнительные элементы управления для включения этих и других сценариев для вашего приложения.

В этой статье описывается, как можно применить и использовать возможности защиты другой экземпляр с экземпляры масштабируемого набора.

> [!NOTE]
>Защита экземпляра в настоящее время находится в общедоступной предварительной версии. Процедура не согласиться необходим для использования общедоступной предварительной версии функций, описанных ниже. Предварительный просмотр защиты экземпляра поддерживается только в том случае, с помощью API версии 2019-03-01 и в масштабируемых наборах, с помощью управляемых дисков.

## <a name="types-of-instance-protection"></a>Типы защиты экземпляра
Масштабируемые наборы предоставляют два типа возможности защиты экземпляра:

-   **Защитить от свертывания**
    - Включить с помощью **protectFromScaleIn** экземпляр набора свойств на шкале
    - Защищает экземпляр из инициированный автомасштабирования
    - (Включая экземпляр delete) операции инициированного пользователем экземпляр **не заблокирован**
    - Операции, инициированные в масштабируемом наборе (обновление, повторно создать образ, освободите т. д.), **не заблокирован**

-   **Защита от масштабируемого набора действий**
    - Включить с помощью **protectFromScaleSetActions** экземпляр набора свойств на шкале
    - Защищает экземпляр из инициированный автомасштабирования
    - Защищает экземпляр из операции, инициированные в масштабируемом наборе (например, обновления пересоздать образ, освобождения, и т.д.)
    - (Включая экземпляр delete) операции инициированного пользователем экземпляр **не заблокирован**
    - Удаление набора масштабирования полный **не заблокирован**

## <a name="protect-from-scale-in"></a>Защитить от свертывания
Защита экземпляра может применяться к экземплярам масштабируемых наборов после создания экземпляров. Защита применяется и изменен только в [модель экземпляра](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) и не на [модель масштабируемого набора](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Существует несколько способов применения защиты свертывания экземплярам масштабируемого набора как описано в приведенных ниже примерах.

### <a name="rest-api"></a>REST API

В следующем примере применяется свертывания защиты для экземпляра в масштабируемом наборе.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>Защита экземпляра поддерживается только в том случае, с помощью API версии 2019-03-01 и более поздних версий

### <a name="azure-powershell"></a>Azure PowerShell

Используйте [AzVmssVM обновления](/powershell/module/az.compute/update-azvmssvm) командлет для настройки защиты уменьшения масштаба масштабируемого набора экземпляра.

В следующем примере применяется свертывания защиты для экземпляра в масштабируемом наборе, зная идентификатор экземпляра 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Используйте [обновление az vmss](/cli/azure/vmss#az-vmss-update) для применения защиты свертывания к экземпляру масштабируемого набора.

В следующем примере применяется свертывания защиты для экземпляра в масштабируемом наборе, зная идентификатор экземпляра 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Защита от масштабируемого набора действий
Защита экземпляра может применяться к экземплярам масштабируемых наборов после создания экземпляров. Защита применяется и изменен только в [модель экземпляра](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) и не на [модель масштабируемого набора](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Также защита экземпляр из масштабируемого набора действий защищает экземпляр от инициированный автомасштабирования.

Существует несколько способов применения масштабирования действия защиты в масштабируемом наборе экземпляров набора как описано в приведенных ниже примерах.

### <a name="rest-api"></a>REST API

В следующем примере применяется защита от масштабируемого набора действий для экземпляра в масштабируемом наборе.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>Защита экземпляра поддерживается только в том случае, с помощью API версии 2019-03-01 и более поздних версий.</br>
Также защита экземпляр из масштабируемого набора действий защищает экземпляр от инициированный автомасштабирования. Нельзя указать «protectFromScaleIn»: false, если параметр «protectFromScaleSetActions»: true

### <a name="azure-powershell"></a>Azure PowerShell

Используйте [AzVmssVM обновления](/powershell/module/az.compute/update-azvmssvm) командлет для применения защиты из масштабируемого набора действий к экземпляру масштабируемого набора.

В следующем примере применяется защита от масштабируемого набора действий для экземпляра в масштабируемом наборе, зная идентификатор экземпляра 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Используйте [обновление az vmss](/cli/azure/vmss#az-vmss-update) Чтобы применить защиту от масштабируемого набора действий к экземпляру масштабируемого набора.

В следующем примере применяется защита от масштабируемого набора действий для экземпляра в масштабируемом наборе, зная идентификатор экземпляра 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Устранение неполадок
### <a name="no-protectionpolicy-on-scale-set-model"></a>Не protectionPolicy в модели масштабируемого набора
Защита экземпляра применяется только в том случае, на экземпляры масштабируемого набора, а не на модели масштабируемого набора.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Не protectionPolicy на экземпляр модели масштабируемого набора
По умолчанию политика защиты не применяется к экземпляру при его создании.

Можно применить защиты экземпляра для экземпляров масштабируемого набора после создания экземпляров.

### <a name="not-able-to-apply-instance-protection"></a>Не удалось применить защиту экземпляра
Защита экземпляра поддерживается только в том случае, с помощью API версии 2019-03-01 и более поздних версий. Проверьте используемую версию API и обновления при необходимости. Может также потребоваться обновить до последней версии PowerShell или интерфейса командной строки.

## <a name="next-steps"></a>Дальнейшие действия
Узнайте, как [развертывать приложение](virtual-machine-scale-sets-deploy-app.md) в масштабируемых наборах виртуальных машин.
