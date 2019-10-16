---
title: Элемент пользовательского интерфейса SizeSelector для портала Azure | Документация Майкрософт
description: Сведения об элементе пользовательского интерфейса Microsoft.Compute.SizeSelector для портала Azure. Используется для выбора размера виртуальной машины.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 288ea7e887a170c8560b0126fa53c9132da35db6
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332668"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Элемент пользовательского интерфейса Microsoft.Compute.SizeSelector
Элемент управления для выбора размера одного или нескольких экземпляров виртуальной машины.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса

Пользователь видит селектор со значениями по умолчанию из определения элемента.

![Элемент пользовательского интерфейса Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

Выбрав элемент управления, пользователь видит развернутое представление доступных размеров.

![Развернутый элемент Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

## <a name="schema"></a>SCHEMA (Схема)
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "options": {
    "hideDiskTypeFilter": false
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Примечания
- В `recommendedSizes` должен быть указан по меньшей мере один размер. По умолчанию используется первый рекомендуемый размер. Список доступных размеров не отсортирован по столбцу "Рекомендуемое состояние". Для сортировки по этому столбцу его нужно щелкнуть.
- Если в выбранном расположении рекомендуемый размер недоступен, он автоматически пропускается. Вместо него используется следующий рекомендуемый размер.
- Параметры `constraints.allowedSizes` и `constraints.excludedSizes` являются необязательными. При этом их нельзя использовать одновременно. Сведения о получении списка доступных размеров виртуальных машин для подписки см. в [этой статье](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Любой размер, не указанный в параметре `constraints.allowedSizes`, скрыт. При этом любой размер, не указанный в параметре `constraints.excludedSizes`, отображается.
- Необходимо задать значение для параметра `osPlatform` (**Windows** или **Linux**). Он используется для определения затрат на оборудование виртуальных машин.
- Параметр `imageReference` не указывается для основных образов, но указывается для сторонних. Он используется для определения затрат на программное обеспечение виртуальных машин.
- Параметр `count` используется для задания соответствующего числа для элемента. Он поддерживает статическое значение, например **2**, или динамическое значение из другого элемента, например `[steps('step1').vmCount]`. Значение по умолчанию — **1**.
- Параметру `numAvailabilityZonesRequired` можно установить значение 1, 2 или 3.
- Значение параметра `hideDiskTypeFilter` по умолчанию — **false**. Фильтр по типу диска позволяет пользователю просматривать все типы дисков или только SSD.

## <a name="sample-output"></a>Пример выходных данных
```json
"Standard_D1"
```

## <a name="next-steps"></a>Дальнейшие действия
* Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
* Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).
