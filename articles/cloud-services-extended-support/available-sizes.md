---
title: Доступные размеры для облачных служб Azure (Расширенная поддержка)
description: Доступные размеры для развертываний облачных служб Azure (Расширенная поддержка)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: c3515b559ef647b9a043a04282b4edfc6e7fa9be
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744655"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Доступные размеры для облачных служб Azure (Расширенная поддержка)

В этой статье описываются доступные размеры виртуальных машин для экземпляров облачных служб (Расширенная поддержка).   

| Семейство SKU |  ACU/Core | 
|---|---|
| [A5–7](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series)| 100 |
|[A8-A11](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | 100 | 
|[D](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#d-series) | 160 | 
|[Dv2](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series) | 160–190* |
|[Dv3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series) | 160–190* |
|[Ev3](https://docs.microsoft.com/azure/virtual-machines/ev3-esv3-series) | 160–190*
|[G](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#g-series) | 180-240 * |
|[H](https://docs.microsoft.com/azure/virtual-machines/h-series) | 290–300* | 

>[!NOTE]
> Единицы ACU, помеченные * , основаны на технологии Intel® Turbo для увеличения частоты ЦП и значительного повышения производительности. Степень увеличения производительности может различаться в зависимости от размера виртуальной машины, рабочей нагрузки и других рабочих нагрузок, выполняющихся на том же узле.

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Настройка размеров для облачных служб (Расширенная поддержка)

Можно указать размер виртуальной машины экземпляра роли как часть модели службы в файле определения службы. Размер роли определяет количество ядер ЦП, объем памяти и размер локальной файловой системы.

Например, установка размера экземпляра веб-роли `Standard_D2` : 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>Изменение размера существующей роли

Чтобы изменить размер существующей роли, измените размер виртуальной машины в файле определения службы (CSDEF), переупакуйте облачную службу и повторно разверните ее. 

## <a name="get-a-list-of-available-sizes"></a>Получение списка доступных размеров 

Чтобы получить список доступных размеров, ознакомьтесь со [списком SKU ресурсов](https://docs.microsoft.com/rest/api/compute/resourceskus/list) и примените следующие фильтры:


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>Следующие шаги 
- Ознакомьтесь с [предварительными требованиями для развертывания](deploy-prerequisite.md) облачных служб (Расширенная поддержка).
- Ознакомьтесь с [часто задаваемыми вопросами](faq.md) о облачных службах (Расширенная поддержка).
- Разверните облачную службу (расширенную поддержку) с помощью [портал Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), [шаблона](deploy-template.md) или [Visual Studio](deploy-visual-studio.md).
