---
title: Коды ошибок для VMs-ит-ми Azure Spot и наборов маштабов
description: Узнайте о кодах ошибок, которые можно увидеть при использовании Spot VMs и маштабах экземпляров.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5a34dc2b9468c6c5af4af0e0addfd8b9ebb7e792
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547820"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>Сообщения об ошибках для Spot VMs и наборов масштабов

Ниже приведены некоторые возможные коды ошибок, которые можно получить при использовании Spot VMs и наборов масштабов.


| Ключ | Сообщение | Описание |
|-----|---------|-------------|
| SkuNotAvailable | Запрошенный уровень для\<\>ресурса ' ресурс '\<\>в настоящее\<время\>недоступен в расположении ' расположение ' для подписки ' subscriptionID '. Пожалуйста, попробуйте другой уровень или развернуть в другом месте. | В этом месте не хватает емкости Azure Spot для создания экземпляра VM или шкалы. |
| ВыселениеПолитикаCanBeSetOnlyOnAzureSpotVirtualMachines  |  Политика выселения может быть установлена только на виртуальных машинах Azure Spot. | Этот VM не является Spot VM, поэтому вы не можете установить политику выселения. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Виртуальная машина Azure Spot не поддерживается в наборе доступности. | Вам нужно выбрать либо использовать Spot VM или использовать VM в наборе доступности, вы не можете выбрать и то, и другое. |
| AzurespotFeaturenotEnabledForSubscription  |  Подписка не включена с функцией Azure Spot. | Используйте подписку, поддерживающую Spot VMs. |
| VMPriorityCannotBeApplied  |  Указанное приоритетное значение '{0}не может{1}быть применено к Виртуальной машине ' , так как приоритет не был указан, когда была создана Виртуальная машина. | Укажите приоритет при создании VM. |
| SpotPriceGreaterInProvidedMaxPrice  |  Невозможно выполнить{0}операцию ' так{1} как предусмотренная максимальная цена{2} 'USD' ниже текущей спотовой цены 'USD' для размера Azure Spot VM'.{3} | Выберите более высокую максимальную цену. Для получения дополнительной информации смотрите информацию о ценах для [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) или [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Недействительное максимальное значение цены. Единственными поддерживаемыми значениями для максимальной цены являются -1 или десятичная больше нуля. Максимальная цена -1 указывает на то, что виртуальная машина Azure Spot не будет выселена по причинам цены. | Введите действительную максимальную цену. Для получения дополнительной информации ознакомьтесь с ценами на [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) или [Windows.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) |
| MaxPriceChangeNotallowedForallocatedVMs | Изменение цены Макса не допускается при выделении VM '{0}в настоящее время. Пожалуйста, разделите и попробуйте еще раз. | Остановите VM так, что вы можете изменить максимальную цену. |
| MaxPriceChangeNotРазрешено | Изменение цены макс не допускается. | Вы не можете изменить максимальную цену для этого VM. |
| AzurespotisnotПоддерживаетсяForthisaPIVersion  |  Azure Spot не поддерживается для этой версии API. | Версия API должна быть 2019-03-01. |
| AzurespotisnotПоддерживаетсяforfor  |  Azure Spot не поддерживается для {0}этого размера VM. | Выберите другой размер VM. Для получения дополнительной информации, см [Spot Виртуальные машины](./linux/spot-vms.md). |
| MaxPriceisSupportedonlyforureurespotvirtualmachines  |  Максимальная цена поддерживается только для виртуальных машин Azure Spot. | Для получения дополнительной информации, см [Spot Виртуальные машины](./linux/spot-vms.md). |
| MoveResourcesWithAzurespotVMNotПоддерживается  |  Запрос ресурсов Move содержит виртуальную машину Azure Spot. В настоящее время это не поддерживается. Пожалуйста, проверьте данные об ошибках для виртуальных идентификаторов машин. | Вы не можете перемещать Spot VMs. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  Запрос ресурсов Move содержит набор виртуальной машины Azure Spot. В настоящее время это не поддерживается. Пожалуйста, проверьте сведения об ошибках для виртуальных идентификаторов набора машин. | Нельзя перемещать экземпляры набора шкалы тоза. |
| ЭфемералОСДискНеподдерживаетсяДляСпотосмы | Эфемерные оС диски не поддерживаются для Spot VMs. | Используйте обычный диск ОС для вашего Spot VM. |
| AzureSpotVMNotSupportedInVmsWithVMOrchestrationmode | Виртуальная машина Azure Spot не поддерживается в виртуальном наборе шкалы машин с режимом VM Orchestration. | Установите режим оркестровки на виртуальную шкалу машины, установленную для использования экземпляров Spot. |


**Следующие шаги** Для получения дополнительной информации, [см.](./linux/spot-vms.md)